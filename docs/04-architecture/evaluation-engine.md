# Evaluation Engine

## Purpose
Automatically evaluate user-submitted code for correctness, efficiency, and code quality, then generate AI feedback.

## Architecture
User submits code → API receives → Celery task enqueued → Worker executes in sandbox → Results stored → AI feedback generated → WebSocket notification


## Evaluation Pipeline (Async – Celery + Redis)

### Step 1: Submission
- User submits code via API (`POST /api/submit/`)
- Submission saved to database (status: `pending`)
- Celery task `evaluate_submission.delay(submission_id)` created
- Frontend immediately receives `task_id` and starts polling (or WebSocket connection)

### Step 2: Sandbox Execution
**Options (free tier):**
- **Piston API** (external, 1000 req/day free) – no setup, supports Python, JavaScript, SQL, Java
- **Docker sandbox** (self-hosted on Render) – more control but requires setup

**We'll use Piston API for MVP.**

**Execution steps:**
1. Piston API receives: language, version, code, stdin (test inputs)
2. Returns stdout, stderr, execution time, memory usage
3. Compare stdout with expected output for each test case

### Step 3: Test Case Validation
- Each question has hidden test cases (not visible to user) + optional public tests
- For each test case:
  - Run code with test input
  - Compare output (exact match for SQL, tolerant for floating point)
  - Record pass/fail, execution time, memory

### Step 4: Score Calculation
| Metric | Weight | How Calculated |
|--------|--------|----------------|
| **Correctness** | 50% | (passed test cases / total test cases) * 50 |
| **Efficiency** | 25% | Based on execution time compared to optimal solution (percentile) |
| **Code Quality** | 25% | Static analysis (PEP 8, cyclomatic complexity, docstrings) via `pylint` or `radon` |

**Total Score = sum(weighted metrics)**

### Step 5: AI Feedback Generation
- After score calculation, trigger **Celery task** `generate_feedback.detail(submission_id)`
- AI receives: user code, test results, expected solution (if available)
- Returns structured feedback (as described in AI architecture)
- Feedback stored in database, linked to submission

### Step 6: Update Analytics & Leaderboard
- Update user skill level (Bayesian moving average)
- Recalculate leaderboard ranking (cached in Redis)
- Update streak, points, badges

## Security & Sandboxing

### Piston API (MVP)
- External service, no setup required
- Automatically isolates execution
- Limits: CPU 1 core, memory 1GB, time 30s

### Future Self-Hosted Docker Sandbox
- Docker containers per execution
- Network disabled, read-only filesystem
- CPU/memory limits via Docker flags
- Timeout handling (SIGKILL after 30s)

## Edge Cases
- **Infinite loops** – Piston API enforces timeouts; fallback uses `timeout` command
- **Memory exhaustion** – limited to 512MB
- **Syntax errors** – captured from stderr, returned to user without evaluation
- **SQL injection** – Piston API runs read-only database snapshot

## Evaluation Queue Scaling
- Celery workers auto-scale on Render (free tier limited to 1 worker)
- Queue backlog monitoring (send alert if >100 pending)
- Prioritize paid users' tasks (future)

## Data Stored per Evaluation
- `submitted_code`, `stdout`, `stderr`, `execution_time`, `memory_used`
- `test_results` (JSON array of pass/fail per test case)
- `score` (total + breakdown)
- `ai_feedback` (JSON with sections: correctness, efficiency, quality, alternatives)
- `created_at`, `status` (pending, completed, failed)
