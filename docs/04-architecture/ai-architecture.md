# AI Architecture

## Purpose
The AI system powers DS-Forge's core differentiators:
- Dynamic question generation (unlimited, non-repetitive)
- Intelligent feedback (correctness, efficiency, code quality, alternatives)
- 4-tier hint system (directional → full answer)
- Revision tour content generation (spaced repetition)
- AI assistant chat

## Components

### Prompt Engine
- Structured prompt templates (few-shot, chain-of-thought)
- Context injection: user skill level, topic, difficulty, dataset schema (for dataset practice)
- Output format enforcement (JSON mode for structured responses)

### AI Model Layer (Primary + Fallback)
| Provider | Model | Use Case | Free Tier Limit |
|----------|-------|----------|-----------------|
| **Google Gemini** (primary) | gemini-1.5-flash | Question generation, feedback, hints | 60 req/min, 1.5M tokens/day |
| **Groq** (fallback) | Llama 3 70B / Mixtral | Fast inference when Gemini is slow/rate-limited | 30 req/min |

**Why two models?** Gemini is free and good quality; Groq is extremely fast for real-time assistant chat.

### Response Processing
- Parse JSON output (error handling if malformed)
- Validate against expected schema
- Sanitize (remove harmful content)
- Format for frontend (markdown, code blocks)

### Caching Layer (Redis – Upstash)
- Cache AI-generated questions for 24 hours (same user, same topic/difficulty → reuse)
- Cache feedback for identical submissions (to avoid redundant API calls)
- Cache revision tour content for 1 hour

### Async Processing (Celery + Redis)
- All AI calls (generation, feedback, hints) run as Celery tasks to avoid blocking the API
- User receives a task ID; frontend polls or uses WebSocket for completion

## AI Workflows (Detailed)

### 1. Dynamic Question Generation
**Input:** `{skill: "Python", topic: "Arrays", difficulty: "Medium", user_skill_level: 5}`
**Prompt template:** *"Generate a coding interview question about {topic} at {difficulty} level. Include: problem statement, example input/output, test cases (hidden), and a hint. Output as JSON."*
**Output:** Question object stored in database, returned to frontend.

### 2. AI Feedback on Submission
**Input:** `{question, user_code, test_results, expected_solution}`
**Prompt:** *"Analyze the user's solution. Provide: correctness analysis, time/space complexity, optimization suggestions, code quality issues, and one alternative approach. Be constructive."*
**Output:** Structured feedback (markdown).

### 3. Hint System (4 tiers)
| Tier | Cost (points) | Content |
|------|---------------|---------|
| Level 1 | 5 | Directional hint (e.g., "Consider using a hash map") |
| Level 2 | 10 | Syntax/function guidance (e.g., "Use `collections.Counter`") |
| Level 3 | 20 | Partial solution (skeleton code with blanks) |
| View Steps | 30 | Step-by-step breakdown |
| View Answer | 50 | Complete solution with explanation |

**Generation:** Each tier uses a different prompt template; cached per question per user.

### 4. Revision Tour Content
**Trigger:** User returns after absence >5 days.
**Input:** User's last 10-day learning history (topics attempted, success rates, struggle points).
**Prompt:** *"Create a 5-minute recap of {topics} with 3 quick quiz questions focusing on weak areas: {struggle_points}. Use spaced repetition principles."*
**Output:** Recap text + quiz questions.

### 5. AI Assistant Chat
**Input:** User's question + current problem context.
**Model:** Groq (fast) for real-time responses.
**Prompt:** *"You are a helpful coding tutor. Help the user understand {concept} or debug {code}. Do not give away full answers unless asked."*

## AI Deployment
- **Backend service:** Django calls Celery tasks → AI workers (separate containers on Render)
- **Rate limiting:** Per-user limits (free tier: 5 AI generations/minute; pro: 30/min)
- **Fallback:** If Gemini fails, retry with Groq; if both fail, return cached question or curated question.
- **Cost control:** Cache aggressively, limit tokens via prompt trimming.

## Monitoring
- Log all AI requests (prompt, response, latency)
- Track rate limit hits, fallback triggers
- User feedback (thumbs up/down) to improve prompts
