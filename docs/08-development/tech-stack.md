
# Technology Stack

## Frontend
| Tool | Version | Purpose |
|------|---------|---------|
| React | 18.x | UI framework |
| Vite | 5.x | Build tool |
| JavaScript | ES6+ | Language |
| Tailwind CSS | 3.x | Styling |
| shadcn/ui | Latest | Component library |
| Monaco Editor | Latest | Code editor |
| Axios | 1.x | HTTP client |
| React Router | 6.x | Routing |

## Backend
| Tool | Version | Purpose |
|------|---------|---------|
| Python | 3.11+ | Language |
| Django | 4.2+ | Web framework |
| Django REST Framework | 3.14+ | API toolkit |
| Celery | 5.3+ | Async task queue |
| Gunicorn | 21.x | WSGI server |

## Database & Cache
| Tool | Version | Purpose |
|------|---------|---------|
| PostgreSQL (Neon) | 15+ | Production database |
| SQL Server | 2019+ | Local development (optional) |
| SQLite | 3.x | Fallback local DB |
| Redis (Upstash) | 7.x | Cache + Celery broker |

## AI & LLM
| Tool | Version | Purpose |
|------|---------|---------|
| Google Gemini API | gemini-1.5-flash | Primary LLM |
| Groq API | Llama 3 70B | Fallback |

## Storage
| Tool | Purpose |
|------|---------|
| Cloudinary | Dataset files, user avatars |

## Code Execution
| Tool | Purpose |
|------|---------|
| Piston API | Sandboxed code execution |

## DevOps & Hosting
| Tool | Purpose |
|------|---------|
| GitHub | Version control |
| GitHub Actions | CI/CD |
| Render | Backend hosting |
| Vercel | Frontend hosting |
| Docker | Containerization |
| Sentry | Error monitoring |

## Testing
| Tool | Purpose |
|------|---------|
| pytest | Backend tests |
| Vitest | Frontend unit tests |
| React Testing Library | Component tests |
| Playwright | End-to-end tests |

## Code Quality
| Tool | Purpose |
|------|---------|
| Black | Python formatter |
| flake8 | Python linter |
| Prettier | JS formatter |
| ESLint | JS linter |
| pre-commit | Git hooks |

## Summary Table
| Layer | Technology |
|-------|------------|
| Frontend | React + Vite + Tailwind + Monaco |
| Backend | Django + DRF + Celery |
| Database | PostgreSQL (Neon) |
| Cache | Redis (Upstash) |
| AI | Gemini + Groq |
| Storage | Cloudinary |
| Hosting | Render + Vercel |
| CI/CD | GitHub Actions |
