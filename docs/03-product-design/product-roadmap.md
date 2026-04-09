# Product Roadmap

## Phase 0: Foundation (Sprint 1-2) – Weeks 1-2
**Goal:** Working authentication and basic practice environment.

- Django project with settings split, JWT auth
- React + Vite + Tailwind setup
- PostgreSQL (Neon) and Redis (Upstash) connections
- CI/CD (GitHub Actions)
- Basic question models and API

## Phase 1: Core Practice (Sprint 3-4) – Weeks 3-4
**Goal:** Users can answer questions and get basic evaluation.

- Monaco editor integration
- Submit answer → Piston API evaluation (test cases)
- Score calculation (correctness only initially)
- Dashboard with simple stats

## Phase 2: AI Integration (Sprint 5-6) – Weeks 5-6
**Goal:** AI-generated questions, intelligent feedback, hint system.

- Gemini API for dynamic question generation
- AI feedback on submitted code (optimization, alternatives)
- 4-tier hint system (directional → full answer)
- AI assistant chat (basic)

## Phase 3: Advanced Features (Sprint 7-8) – Weeks 7-8
**Goal:** Revision tours, dataset practice, gamification.

- Revision tour system (absence detection, spaced repetition recap)
- Dataset upload (Cloudinary) and dataset-specific AI questions
- Leaderboard (global + topic-wise) with Redis caching
- Skill analytics dashboard (radar chart, progress over time)
- Points, streaks, badges

## Phase 4: Testing & Hardening (Sprint 9) – Week 9
**Goal:** Production-ready quality.

- Unit, integration, E2E tests (Playwright)
- Security scan (bandit, npm audit)
- Performance test (k6 – 100 concurrent users)
- Bug fixes and polish

## Phase 5: MVP Launch (Sprint 10) – Week 10
**Goal:** Public launch.

- Deploy backend to Render (Docker)
- Deploy frontend to Vercel
- Configure Sentry error tracking
- Soft launch with 10 beta users → iterate → public launch

## Post-MVP (Months 4-6)

### Phase 6: B2C Growth
- Referral system, social sharing
- Email notifications (weekly progress reports)
- Advanced analytics (skill decay predictions)
- More AI models (Groq as fallback)

### Phase 7: B2B Enterprise
- Team/organization accounts
- Role-based access control (admin, instructor, student)
- API for third-party integration
- Custom company question banks
- SSO (Google, GitHub, LinkedIn)

### Phase 8: AI Interview Simulator
- Mock interviews with AI interviewer
- Real-time voice and code evaluation
- Recorded sessions for review

## Success Metrics After Launch
- Monthly Active Users (MAU) > 500
- Pro subscription conversion > 5%
- User retention (30-day) > 40%
- NPS > 50
