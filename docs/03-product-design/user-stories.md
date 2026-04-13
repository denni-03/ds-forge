
# User Stories – MVP

All user stories follow the format:  
**As a [user], I want to [action], so that [benefit].**

---

## Authentication & Profile

### US‑01: User Registration
**As a** new user,  
**I want to** create an account using username, email and password,  
**so that** I can access the platform.

**Acceptance Criteria:**
- Email must be unique.
- Password stored with strong hashing (bcrypt / Django default).
- Successful registration returns user ID (no auto‑login).

### US‑02: User Login
**As a** registered user,  
**I want to** log in with username/email and password,  
**so that** I can access my dashboard and practice.

**Acceptance Criteria:**
- Valid credentials return a JWT access token and refresh token.
- Invalid credentials return `401 Unauthorized`.
- Token expiry set to 60 minutes.

### US‑03: View Profile
**As a** logged‑in user,  
**I want to** see my profile (username, email, skill level, total points),  
**so that** I can track my progress.

**Acceptance Criteria:**
- Profile endpoint returns user data (excluding password hash).
- Only the authenticated user can access their own profile.

### US‑04: Update Profile (Basic)
**As a** logged‑in user,  
**I want to** update my skill level (1‑10),  
**so that** the system can adjust question difficulty.

**Acceptance Criteria:**
- PATCH `/api/users/profile/` with `skill_level` field.
- Validation: integer between 1 and 10.

---

## Practice & Questions

### US‑05: Select Question Parameters
**As a** user,  
**I want to** choose a skill (Python, SQL), topic, and difficulty,  
**so that** I practice relevant questions.

**Acceptance Criteria:**
- Endpoint `GET /api/questions/?skill=&topic=&difficulty=` returns list of matching questions.
- If no filter, return all questions (paginated).
- Supports at least 100 curated questions.

### US‑06: Generate AI Question
**As a** user,  
**I want to** generate a new AI question on a selected topic/difficulty,  
**so that** I get unlimited fresh practice.

**Acceptance Criteria:**
- Async endpoint `POST /api/questions/generate-ai/` returns `task_id`.
- Poll `/api/tasks/{id}/` for result.
- Result contains question object (title, description, test cases).
- AI model: Gemini API (fallback to curated if fails).

### US‑07: Submit Solution
**As a** user,  
**I want to** submit my code for a question,  
**so that** the system can evaluate it.

**Acceptance Criteria:**
- `POST /api/submissions/` with `question_id` and `code`.
- Returns `submission_id` and `task_id`.
- Evaluation runs asynchronously (Celery).

### US‑08: View Evaluation Result
**As a** user,  
**I want to** see if my solution passed test cases and my score,  
**so that** I know my performance.

**Acceptance Criteria:**
- `GET /api/submissions/{id}/` returns status (`pending`, `completed`).
- When completed, includes:
  - correctness score (0‑100)
  - total score (0‑100)
  - AI feedback (text)
  - execution time

### US‑09: Receive AI Feedback
**As a** user,  
**I want to** receive intelligent feedback on my solution,  
**so that** I can improve my code quality and approach.

**Acceptance Criteria:**
- AI feedback includes:
  - correctness analysis (what passed/failed)
  - one optimization tip (e.g., “use dictionary for O(n)”)
  - one alternative approach (optional for MVP)
- Feedback stored with score.

---

## Hints & Gamification

### US‑10: Request Hint (Tier 1)
**As a** user stuck on a problem,  
**I want to** request a directional hint,  
**so that** I can get unstuck without seeing the full solution.

**Acceptance Criteria:**
- Hint button appears after 60 seconds of inactivity (frontend) or manual request.
- `POST /api/hints/` with `attempt_id` and `level=1`.
- Costs 5 points (deduct from user’s total_points).
- Response contains a one‑sentence hint.
- Cooldown: 30 seconds before next hint.

### US‑11: Leaderboard
**As a** user,  
**I want to** see my rank and top users by total score,  
**so that** I can compete and stay motivated.

**Acceptance Criteria:**
- `GET /api/leaderboard/` returns list of top 100 users (username, total_score, rank).
- Ranking computed from aggregated scores of all attempts.
- Updated after each submission.

---

## Admin (Custom Role)

### US‑12: Promote/Demote Admin
**As an** admin user,  
**I want to** promote or demote other users to admin role,  
**so that** I can manage platform administration.

**Acceptance Criteria:**
- `POST /api/admin/users/{id}/toggle-admin/` – requires `is_admin=true` on requesting user.
- Changes `is_admin` flag on target user.
- Non‑admins cannot access endpoint (403 Forbidden).

### US‑13: List Users (Admin)
**As an** admin user,  
**I want to** see a list of all registered users,  
**so that** I can manage accounts.

**Acceptance Criteria:**
- `GET /api/admin/users/` returns paginated list (id, username, email, is_admin, total_points).
- Access restricted to admins only.
