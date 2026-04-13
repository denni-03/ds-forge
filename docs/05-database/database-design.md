# Database Design

## Overview
DS-Forge uses **SQL Server for local development** (or SQLite fallback) and **Neon PostgreSQL for production** (cloud, free tier). The schema is identical across both.

---

## Core Tables

### Users (Custom User Model – Extends AbstractUser)
| Field | Type | Description |
|-------|------|-------------|
| id | PK | Auto-increment |
| username | string(150), unique | |
| email | string(255), unique | |
| password_hash | string(128) | |
| bio | text | Optional |
| avatar_url | string(500) | Cloudinary URL |
| skill_level | int (1-10) | Overall skill score |
| total_points | int | Accumulated points |
| streak_days | int | Current practice streak |
| last_active | datetime | Last activity timestamp |
| created_at | datetime | |
| updated_at | datetime | |

### Skills
| Field | Type |
|-------|------|
| id | PK |
| name | string(50) | e.g., "Python", "SQL", "JavaScript" |

### Topics
| Field | Type |
|-------|------|
| id | PK |
| skill_id | FK → skills.id |
| name | string(100) | e.g., "Arrays", "Joins", "Closures" |

### Questions (Curated + AI-Generated)
| Field | Type | Description |
|-------|------|-------------|
| id | PK | |
| title | string(200) | |
| description | text | Problem statement |
| skill_id | FK → skills.id | |
| topic_id | FK → topics.id | |
| difficulty | enum('easy','medium','hard') | |
| type | enum('coding','sql','mcq') | |
| is_ai_generated | boolean | |
| prompt_used | text | If AI-generated, store prompt |
| test_cases | JSON | Array of {input, expected} |
| expected_output | text | For SQL/MCQ |
| points | int | Base points for solving |
| created_at | datetime | |

### AI Generated Questions (Audit Log)
| Field | Type |
|-------|------|
| id | PK |
| user_id | FK → users.id |
| skill_id | FK → skills.id |
| topic_id | FK → topics.id |
| difficulty | string |
| generated_question | JSON | Full question object |
| created_at | datetime |

### Attempts
| Field | Type |
|-------|------|
| id | PK |
| user_id | FK → users.id |
| question_id | FK → questions.id |
| submitted_code | text |
| execution_time | float (seconds) |
| memory_used | int (MB) |
| status | enum('pending','success','failed') |
| created_at | datetime |

### Scores
| Field | Type |
|-------|------|
| id | PK |
| attempt_id | FK → attempts.id |
| correctness_score | float (0-100) |
| efficiency_score | float (0-100) |
| quality_score | float (0-100) |
| total_score | float (0-100) |
| ai_feedback | JSON |

### Datasets
| Field | Type |
|-------|------|
| id | PK |
| user_id | FK → users.id |
| name | string(200) |
| description | text |
| file_url | string(500) | Cloudinary URL |
| schema | JSON | Column names, types |
| row_count | int |
| size | int (bytes) |
| created_at | datetime |

### Dataset Questions (AI-generated from dataset)
| Field | Type |
|-------|------|
| id | PK |
| dataset_id | FK → datasets.id |
| question_text | text |
| difficulty | string |
| created_at | datetime |

### Revision Tours
| Field | Type |
|-------|------|
| id | PK |
| user_id | FK → users.id |
| absence_days | int |
| topics_covered | JSON | Array of topics |
| struggle_points | JSON | Questions with multiple attempts |
| recap_content | text |
| quiz_results | JSON |
| completed | boolean |
| created_at | datetime |

### Leaderboard
| Field | Type |
|-------|------|
| id | PK |
| user_id | FK → users.id (unique) |
| total_score | float |
| rank | int (computed) |
| updated_at | datetime |

### Hint Usage
| Field | Type |
|-------|------|
| id | PK |
| user_id | FK → users.id |
| attempt_id | FK → attempts.id |
| hint_level | int (1-4) |
| points_spent | int |
| created_at | datetime |

---

## Indexes for Performance

```sql
CREATE INDEX idx_user_skill ON users(skill_level);
CREATE INDEX idx_question_topic ON questions(topic_id, difficulty);
CREATE INDEX idx_attempts_user ON attempts(user_id, created_at);
CREATE INDEX idx_scores_attempt ON scores(attempt_id);
CREATE INDEX idx_leaderboard_score ON leaderboard(total_score DESC);
CREATE INDEX idx_questions_skill ON questions(skill_id);
CREATE INDEX idx_datasets_user ON datasets(user_id);
