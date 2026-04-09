# Feature List (Prioritized for MVP)

## Core Features (Sprint 1-6)

### User & Profile
- [x] JWT authentication (register, login, logout)
- [x] User profile (bio, avatar, skill level)
- [x] Points and streaks tracking

### Practice Engine
- [ ] Dynamic AI-generated questions (Gemini API) – unlimited
- [ ] Curated question bank (fallback)
- [ ] Select skill (Python, SQL, JavaScript, Algorithms) / topic / difficulty
- [ ] Monaco code editor with syntax highlighting
- [ ] Console output display
- [ ] Submit answer → async evaluation (Celery + Redis)

### Evaluation & Feedback
- [ ] Automated test case execution (sandbox via Piston API or Docker)
- [ ] Score calculation (correctness, efficiency, code quality)
- [ ] AI-generated feedback (optimization tips, alternative solutions, best practices)
- [ ] Hint system (4 tiers: directional, syntax, partial solution, full answer) – points-based
- [ ] View steps / view answer (spend points)

### Revision & Retention
- [ ] Absence detection (>5 days inactive)
- [ ] Revision tour – recap of last learning session with spaced repetition
- [ ] Quick quizzes on weak topics

### Dataset Practice
- [ ] Upload CSV/Excel datasets (Cloudinary)
- [ ] Dataset preview (first 5 rows, schema)
- [ ] AI generates questions specific to uploaded dataset
- [ ] Practice with dataset in same code editor

### Analytics & Gamification
- [ ] Dashboard: skill radar chart, progress over time, recent attempts
- [ ] Leaderboard (global, topic-wise, weekly)
- [ ] Points system (solving, perfect solution, streaks)
- [ ] Badges (milestone-based)

### Deployment & Monitoring
- [ ] CI/CD (GitHub Actions)
- [ ] Backend hosting on Render (Docker)
- [ ] Frontend hosting on Vercel
- [ ] Error tracking with Sentry

## Post-MVP Features (Future)
- AI interview simulator (mock interviews)
- Team/organization accounts (B2B)
- Custom company question banks
- API for third-party integration
- Mobile app (React Native)
- ML model training challenges
- Community discussions and solution sharing
