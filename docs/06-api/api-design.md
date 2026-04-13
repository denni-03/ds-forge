# API Design

## Base URL
`/api/v1/`

## Authentication
JWT tokens (access + refresh). Include in header:
`Authorization: Bearer <access_token>`

---

## Endpoints

### Authentication
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/auth/register/` | Create new user |
| POST | `/auth/login/` | Get tokens + user data |
| POST | `/auth/refresh/` | Refresh access token |
| GET | `/auth/profile/` | Get current user |
| PUT/PATCH | `/auth/profile/` | Update profile |

### Questions
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/questions/` | List questions (filter by skill, topic, difficulty) |
| POST | `/questions/generate-ai/` | Generate new AI question (async → returns task_id) |
| GET | `/questions/{id}/` | Get question details |

### Practice & Submissions
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/submissions/` | Submit code for evaluation (async → returns task_id) |
| GET | `/submissions/{id}/` | Get submission result |
| GET | `/submissions/` | List user's submissions |

### Hints (4 tiers, points-based)
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/hints/` | Request hint level 1-4 (spends points) |
| POST | `/hints/view-steps/` | Request step-by-step breakdown (costs 30 pts) |
| POST | `/hints/view-answer/` | Request full answer (costs 50 pts) |

### Datasets
| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/datasets/upload/` | Upload CSV file (Cloudinary) |
| GET | `/datasets/` | List user's datasets |
| GET | `/datasets/{id}/preview/` | First 5 rows + schema |
| POST | `/datasets/{id}/generate-questions/` | AI generates questions from dataset (async) |

### Revision Tours
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/revision/check/` | Check if user needs revision tour (returns tour if absent >5 days) |
| POST | `/revision/generate/` | Force generate revision tour (async) |
| POST | `/revision/complete/` | Mark tour as completed |

### Leaderboard
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/leaderboard/` | Global leaderboard (top 100) |
| GET | `/leaderboard/topic/{topic_id}/` | Topic-specific rankings |
| GET | `/leaderboard/me/` | Current user rank |

### Analytics
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/analytics/skills/` | Radar chart data (skill levels per topic) |
| GET | `/analytics/progress/` | Historical performance graph |
| GET | `/analytics/recent/` | Last 10 attempts with feedback |

### Async Task Status
| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/tasks/{task_id}/` | Poll task status (pending, completed, failed) |

---

## Request / Response Examples

### Register
```json
POST /auth/register/
{
  "username": "johndoe",
  "email": "john@example.com",
  "password": "SecurePass123",
  "password2": "SecurePass123"
}
// Response 201
{
  "id": 1,
  "username": "johndoe",
  "email": "john@example.com"
}

**
Login**


POST /auth/login/
{
  "username": "johndoe",
  "password": "SecurePass123"
}
// Response 200
{
  "access": "eyJhbGc...",
  "refresh": "eyJhbGc...",
  "user": {
    "id": 1,
    "username": "johndoe",
    "skill_level": 3,
    "total_points": 120
  }
}
**
Generate AI Question (Async)**

POST /questions/generate-ai/
{
  "skill": "Python",
  "topic": "Arrays",
  "difficulty": "medium"
}
// Response 202
{
  "task_id": "abc-123-def",
  "status_url": "/tasks/abc-123-def/"
}

**Poll Task Status**
GET /tasks/abc-123-def/
// Pending
{ "status": "pending" }
// Completed
{
  "status": "completed",
  "result": {
    "question_id": 42,
    "title": "Find Duplicate",
    "description": "...",
    "test_cases": [...]
  }
}

**Submit Code (Async)**

POST /submissions/
{
  "question_id": 42,
  "code": "def solve(arr):\n    return max(arr)"
}
// Response 202
{
  "task_id": "xyz-789",
  "submission_id": 101
}

**Submission Result (after polling)**

GET /submissions/101/
{
  "id": 101,
  "question_id": 42,
  "status": "completed",
  "score": 85,
  "feedback": {
    "correctness": 90,
    "efficiency": 80,
    "quality": 85,
    "ai_feedback": "Your solution is correct. Consider using a hash map for O(n) instead of O(n²)."
  }
}

**Request Hint (Level 1)**

POST /hints/
{
  "attempt_id": 101,
  "level": 1
}
// Response 200
{
  "hint": "Think about using a dictionary to count frequencies.",
  "points_spent": 5,
  "remaining_points": 115
}

**Upload Dataset**

POST /datasets/upload/
Content-Type: multipart/form-data
file: dataset.csv
// Response 201
{
  "dataset_id": 5,
  "name": "dataset.csv",
  "row_count": 1000,
  "schema": ["customer_id", "revenue", "date"]
}

**Revision Tour Check**
GET /revision/check/
// If absent >5 days
{
  "needs_revision": true,
  "absence_days": 12,
  "tour": {
    "topics": ["Python Lists", "SQL Joins"],
    "struggle_points": ["Question #33 (multiple attempts)"],
    "recap": "Last session you practiced...",
    "quiz": [...]
  }
}

**Leaderboard**

GET /leaderboard/
[
  { "rank": 1, "username": "alice", "total_score": 9850 },
  { "rank": 2, "username": "bob", "total_score": 9720 }
]

**Analytics – Skills Radar**

GET /analytics/skills/
{
  "Python": 75,
  "SQL": 60,
  "JavaScript": 40,
  "Algorithms": 55
}

**Error Responses**
{
  "error": "Invalid credentials",
  "code": 401
}

**Rate Limiting**

Free tier: 100 requests/minute per user.

Pro tier: 500 requests/minute.

Headers: X-RateLimit-Limit, X-RateLimit-Remaining, Retry-After.


---

## ✅ Action Item

**Replace your existing `docs/06-api/api-design.md` with the content above.**

Then commit:

```cmd
git add docs/06-api/api-design.md
git commit -m "Expand API design with all endpoints: auth, questions, submissions, hints, datasets, revision tours, leaderboard, analytics, async tasks"
git push origin main
