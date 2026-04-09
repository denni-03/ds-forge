# MVP Scope (6 Sprints, ~10 Weeks)

## Objective
Build a fully functional AI-powered technical practice platform that allows job seekers to practice coding and data problems with AI-generated questions, intelligent feedback, and spaced repetition revision tours.

## MVP Features (All Must Be Included)

| Feature Area | Specific Features |
|--------------|-------------------|
| **Authentication** | JWT register, login, profile |
| **Practice Engine** | Skill/topic/difficulty selection, AI-generated questions (Gemini), code editor (Monaco), submit, async evaluation |
| **Evaluation** | Test case execution (Piston API), score calculation (correctness, efficiency), AI feedback |
| **Hint System** | 4 tiers (directional → full answer), points-based |
| **Revision Tours** | Absence detection, recap generation, spaced repetition quiz |
| **Dataset Practice** | Upload CSV, dataset preview, AI generates dataset-specific questions |
| **Analytics** | Dashboard with skill radar chart, progress graph, recent attempts |
| **Leaderboard** | Global and topic-wise rankings, points system |
| **Deployment** | Render (backend), Vercel (frontend), GitHub Actions CI/CD, Sentry monitoring |

## Features Explicitly Excluded from MVP (Post-MVP)
- ML model training challenges
- Collaborative coding / pair programming
- Community discussion forums
- B2B enterprise features (teams, SSO, API)
- Mobile app
- AI interview simulator

## Success Criteria for MVP Launch
- [ ] 100 daily active users within 3 months
- [ ] Average session time >15 minutes
- [ ] User satisfaction score (NPS) > 40
- [ ] AI feedback quality rating >4/5
- [ ] Revision tours completed by >50% of returning users
- [ ] Zero critical bugs after launch week

## MVP Timeline (Detailed Sprints)

| Sprint | Weeks | Focus | Deliverable |
|--------|-------|-------|-------------|
| Sprint 1 | 1-2 | Foundation | Django auth, SQL Server/Neon, React setup, CI |
| Sprint 2 | 3-4 | Core Practice | Question models, Monaco editor, basic evaluation (Piston) |
| Sprint 3 | 5-6 | AI Integration | Gemini question generation, AI feedback, hint system |
| Sprint 4 | 7-8 | Advanced | Revision tours, dataset practice, leaderboard, analytics |
| Sprint 5 | 9 | Testing | Unit, integration, E2E, security, performance |
| Sprint 6 | 10 | Deployment | Render, Vercel, Sentry, soft launch |

## Resource Requirements (Self-funded, free tiers)
- GitHub (private repo)
- Neon PostgreSQL (free 1GB)
- Upstash Redis (free 10k commands/day)
- Cloudinary (free 25GB)
- Gemini API (free 60 req/min)
- Render (free tier)
- Vercel (free tier)
- Sentry (free 5k events/month)

No budget required for MVP.
