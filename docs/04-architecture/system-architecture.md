# System Architecture

## High-Level Overview (Production-Grade)

User → Frontend (Vercel) → API Gateway (Django/DRF) → Backend Services → Data Layer
↓
Async Queue (Celery + Redis)
↓
Worker Services
(Code Exec, AI, Email)
↓
External APIs (Gemini, Groq)


## Component Details

### 1. Frontend (Vercel – Free Tier)
- **Framework:** React 18 + Vite + JavaScript (no TS for now)
- **UI:** Tailwind CSS + shadcn/ui (professional components)
- **Code Editor:** Monaco Editor (VS Code's editor)
- **State:** React Context + useReducer
- **HTTP:** Axios with JWT interceptors
- **Real-time:** WebSocket (or polling) for evaluation status
- **PWA:** Service workers for offline mode (future)

### 2. API Gateway (Django + DRF – Render Free Tier)
- **Authentication:** JWT (access + refresh tokens)
- **Rate limiting:** `django-ratelimit` (free tier: 100 req/min per user)
- **CORS:** Configured for Vercel frontend
- **API Docs:** drf-spectacular (Swagger UI at `/api/docs`)
- **Versioning:** URL-based (`/api/v1/`)

### 3. Application Services (Django Apps)
| Service | Responsibilities |
|---------|------------------|
| `users` | Registration, login, profile, JWT |
| `questions` | Curated + AI-generated questions, CRUD, tagging |
| `practice` | User attempts, submissions, session management |
| `evaluation` | Score calculation, test case validation |
| `ai` | Gemini/Groq integration, prompt engineering, caching |
| `datasets` | Upload (Cloudinary), preview, dataset-specific generation |
| `revision` | Absence detection, tour generation, spaced repetition |
| `analytics` | Skill radar, progress graphs, leaderboard |
| `leaderboard` | Real-time rankings (Redis-backed) |

### 4. Async Task Queue (Celery + Upstash Redis)
- **Broker:** Upstash Redis (free 10k commands/day)
- **Result backend:** Same Redis instance
- **Task types:**
  - `evaluate_submission` – runs code in sandbox
  - `generate_ai_question` – calls Gemini API
  - `generate_feedback` – AI feedback on submission
  - `generate_revision_tour` – spaced repetition content
  - `send_email_notification`

### 5. Worker Services (Separate Containers on Render)
| Worker | Purpose |
|--------|---------|
| **Code Executor** | Runs user code via Piston API or Docker sandbox |
| **AI Worker** | Calls Gemini/Groq, caches results |
| **Email Worker** | Sends notifications (future) |

### 6. Data Layer

#### PostgreSQL (Neon – Free 1GB)
- **Tables:** users, questions, attempts, scores, datasets, revision_tours, leaderboard_entries
- **Indexes:** on user_id, question_id, created_at, topic
- **Full-text search:** for dataset search (using PostgreSQL built-in)

#### Redis (Upstash – Free 10k commands/day)
- **Cache:** session storage, leaderboard (sorted sets), rate limiting counters, AI response cache
- **Queue:** Celery task broker

#### Cloudinary (Free 25GB)
- **Stores:** Dataset CSV files, user avatars, generated visualizations

### 7. External Services
| Service | Purpose | Free Tier Limit |
|---------|---------|-----------------|
| **Gemini API** | AI question generation, feedback, hints | 60 req/min, 1.5M tokens/day |
| **Groq API** | Fallback AI, fast assistant chat | 30 req/min |
| **Piston API** | Code execution (sandbox) | 1000 req/day |
| **Sentry** | Error tracking (frontend + backend) | 5k events/month |

### 8. Deployment Infrastructure

#### CI/CD (GitHub Actions)
- On push to `main`: lint → test → build Docker image → deploy to Render (backend) + Vercel (frontend)

#### Hosting
| Component | Platform | Free Tier Details |
|-----------|----------|-------------------|
| Backend API | Render | Web Service + Worker (sleeps after 15 mins, wakes on request) |
| Frontend | Vercel | Automatic deploys from GitHub, global CDN |
| Database | Neon PostgreSQL | 1GB, auto-suspend after 5 minutes |
| Redis | Upstash | 10k commands/day, global |

### 9. Monitoring & Observability
- **Errors:** Sentry (captures exceptions in Django and React)
- **Logs:** Render's built-in logging + console output (can send to ELK later)
- **Uptime:** Render's health check endpoint (`/health/`)
- **Performance:** P95 latency from API logs (manual analysis)

## Data Flow Example (User Practices a Question)

1. User logs in → Frontend stores JWT in localStorage.
2. User selects "Python, Arrays, Medium" → Frontend calls `POST /api/questions/generate/` with body `{skill, topic, difficulty}`.
3. Django API creates Celery task `generate_ai_question.delay(...)`, returns `task_id`.
4. Frontend polls `/api/tasks/{task_id}/` (or WebSocket) until question is ready.
5. User writes code in Monaco editor → clicks Submit → Frontend calls `POST /api/submit/` with code and question_id.
6. Django saves submission (status=pending), enqueues `evaluate_submission.delay(submission_id)`, returns `task_id`.
7. Celery worker executes:
   - Calls Piston API to run code against test cases.
   - Calculates score.
   - Enqueues `generate_feedback.detail()`.
8. AI feedback is generated via Gemini, stored.
9. Frontend polls and receives results → displays score, feedback, updates leaderboard.
10. User finishes session → dashboard updates skill radar.

## Scalability Considerations (Free Tier Limits)
- **Concurrent users:** ~50-100 (Render's free tier CPU/memory)
- **Daily API requests:** ~10k (limited by Piston API and Gemini)
- **Database size:** 1GB (Neon) – sufficient for 10k users
- **Redis commands:** 10k/day – fine for MVP
- **Mitigation:** Cache aggressively, rate limit users, optimize queries.
