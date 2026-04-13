# Technical Specification – MVP

## 1. Overview

This document maps user stories to technical components. It is the **handoff from Business Development to Architecture**.

| User Story | API Endpoint | Service / Logic | Database Model |
|------------|--------------|----------------|----------------|
| US‑01 Register | `POST /api/auth/register/` | `users.services.create_user()` | `User` |
| US‑02 Login | `POST /api/auth/login/` | `rest_framework_simplejwt` | `User` |
| US‑03 View Profile | `GET /api/users/profile/` | `users.services.get_profile()` | `User` |
| US‑04 Update Profile | `PATCH /api/users/profile/` | `users.services.update_profile()` | `User` |
| US‑05 Select Questions | `GET /api/questions/` | `questions.services.filter_questions()` | `Question`, `Skill`, `Topic` |
| US‑06 Generate AI Question | `POST /api/questions/generate-ai/` → Celery task | `ai.services.generate_question()` | `Question` (AI‑generated) |
| US‑07 Submit Solution | `POST /api/submissions/` → Celery task | `evaluation.services.evaluate_code()` | `Attempt`, `Score` |
| US‑08 View Result | `GET /api/submissions/{id}/` | `evaluation.services.get_result()` | `Attempt`, `Score` |
| US‑09 AI Feedback | (part of US‑08) | `ai.services.generate_feedback()` | `Score.ai_feedback` |
| US‑10 Hint | `POST /api/hints/` | `gamification.services.give_hint()` | `HintUsage`, `User.points` |
| US‑11 Leaderboard | `GET /api/leaderboard/` | `leaderboard.services.get_ranking()` | Computed from `Score` |
| US‑12 Toggle Admin | `POST /api/admin/users/{id}/toggle-admin/` | `admin.services.toggle_admin()` | `User.is_admin` |
| US‑13 List Users | `GET /api/admin/users/` | `admin.services.list_users()` | `User` |

---

## 2. MVP Database Schema

See `docs/05-database/database-design.md` (final version will be updated after approval). Core tables:

- `User` (id, username, email, password_hash, skill_level, total_points, is_admin, ...)
- `Skill`, `Topic`
- `Question` (curated + AI‑generated)
- `Attempt`
- `Score`
- `HintUsage`

No revision tour, dataset, or assignment tables in MVP.

---

## 3. Non‑Functional Requirements (NFRs)

| Requirement | Target | Implementation Notes |
|-------------|--------|----------------------|
| API response time (read) | < 200ms (p95) | Use database indexes, caching (Redis) for leaderboard. |
| API response time (write) | < 500ms (p95) | Async tasks for AI/evaluation. |
| AI generation | < 5 seconds | Use Gemini API, fallback to Groq; cache results per user per day. |
| Concurrent users | 100 | Free tier on Render supports ~50; use queue to smooth load. |
| Uptime | 99% (MVP) | Health check endpoint; Render auto‑restart. |
| Security | JWT, HTTPS, input validation | Standard Django + DRF protections. |

---

## 4. Service Layer Design

We will organise backend code into **services** (business logic) separate from views.

| Service | Location | Responsibilities |
|---------|----------|------------------|
| `UserService` | `users/services.py` | create_user, update_profile, deduct_points, check_admin |
| `QuestionService` | `questions/services.py` | filter questions, get_random, store_ai_question |
| `AIService` | `ai/services.py` | call Gemini API, parse response, cache results |
| `EvaluationService` | `evaluation/services.py` | run test cases (Piston API), calculate score, store attempt |
| `LeaderboardService` | `leaderboard/services.py` | compute ranking (Redis or SQL), return top N |
| `AdminService` | `admin/services.py` | list users, promote/demote |

Views will be thin – they call services, handle HTTP requests/responses.

---

## 5. Async Task Design (Celery)

| Task Name | Queue | Called By | Description |
|-----------|-------|-----------|-------------|
| `generate_ai_question` | `ai` | `POST /api/questions/generate-ai/` | Calls Gemini, creates Question object, returns ID. |
| `evaluate_submission` | `evaluation` | `POST /api/submissions/` | Calls Piston API, stores Attempt & Score, triggers feedback. |
| `generate_feedback` | `ai` | `evaluate_submission` (chain) | After evaluation, calls AI to generate feedback text. |

We will use **Upstash Redis** as broker (free tier).

---

## 6. API Endpoints (MVP)

All endpoints under `/api/v1/`.

| Method | Endpoint | Auth | Description |
|--------|----------|------|-------------|
| POST | `/auth/register/` | No | Register new user |
| POST | `/auth/login/` | No | Obtain JWT tokens |
| GET | `/auth/profile/` | Yes | Get own profile |
| PATCH | `/auth/profile/` | Yes | Update profile (skill_level) |
| GET | `/questions/` | Yes | List questions with filters |
| POST | `/questions/generate-ai/` | Yes | Start AI question generation (async) |
| GET | `/tasks/{task_id}/` | Yes | Poll async task status |
| POST | `/submissions/` | Yes | Submit code (async) |
| GET | `/submissions/{id}/` | Yes | Get submission result |
| POST | `/hints/` | Yes | Request tier 1 hint (costs points) |
| GET | `/leaderboard/` | Yes | Get top 100 users |
| GET | `/admin/users/` | Admin only | List all users |
| POST | `/admin/users/{id}/toggle-admin/` | Admin only | Promote/demote user |

---

## 7. Development Phases (Sprints)

| Sprint | Focus | Key Deliverables |
|--------|-------|------------------|
| 1 | Project setup + Auth | Django with settings split, SQL Server connection, JWT, User model. |
| 2 | Question & practice | Question models, seed curated questions, API to list/filter. |
| 3 | Evaluation & scoring | Piston API integration, test case execution, Score model, basic feedback. |
| 4 | AI integration | Gemini API, AI question generation, AI feedback (async tasks). |
| 5 | Hints, leaderboard, admin | Hint endpoint, points deduction, leaderboard API, admin APIs. |
| 6 | Testing & deployment | Unit tests, CI/CD, deploy to Render + Vercel, documentation. |

---

## 8. Acceptance Criteria for MVP Completion

- [ ] All user stories US‑01 to US‑13 are implemented and tested.
- [ ] API documentation available at `/api/docs/` (Swagger).
- [ ] Backend unit test coverage > 70%.
- [ ] Deployment to Render (backend) and Vercel (frontend) works.
- [ ] Demo script ready: register → login → generate AI question → submit code → see evaluation & AI feedback.
