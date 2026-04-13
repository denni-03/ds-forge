# Prompt Design for Gemini API

## Overview
All AI features use Google Gemini (gemini-1.5-flash) with structured prompts and JSON output parsing.

---

## Prompt Templates

### 1. Question Generation
**System prompt:**
You are an expert technical interviewer. Generate a unique coding problem.


**User prompt:**

Skill: {skill}
Topic: {topic}
Difficulty: {difficulty}
User skill level: {user_skill_level} (1-10)

Generate a coding interview question. Output JSON:
{
"title": "short title",
"description": "problem statement with examples",
"test_cases": [{"input": "...", "expected": "..."}],
"hint": "initial hint",
"points": 50
}


### 2. AI Feedback on Submission
**System prompt:**

You are a senior code reviewer. Provide constructive feedback.


**User prompt:**

Question: {question_text}
User code: {user_code}
Test results: {test_results} (e.g., "3/5 passed")
Expected solution (optional): {expected_solution}

Provide feedback in JSON:
{
"correctness": "analysis of passed/failed tests",
"efficiency": "time/space complexity estimate and improvement suggestions",
"code_quality": "naming, structure, best practices",
"alternative": "different approach with pseudocode",
"overall": "summary"
}


### 3. Hint Generation (Level 1-4)
**System prompt:**

You are a helpful tutor. Give progressive hints without revealing full answer.


**Level 1 (directional):**

Give a one-sentence hint about which data structure or algorithm to consider.


**Level 2 (syntax/function):**

Suggest specific functions or syntax to use. Provide a short example snippet.


**Level 3 (partial solution):**

Provide skeleton code with 1-2 lines missing. User fills in the blanks.


**Level 4 (full solution) – not generated via AI, stored pre-written.

### 4. Revision Tour Content
**System prompt:**

You are a learning coach. Create a spaced repetition recap.


**User prompt:**

User's last 10-day learning:
Topics attempted: {topics_with_success_rates}
Struggle points: {questions_with_multiple_attempts}

Generate a 5-minute revision tour including:

Brief recap of each topic (2 sentences)

3 quick quiz questions focusing on weak areas

Encouraging message


### 5. AI Assistant Chat
**System prompt:**

You are a coding tutor. Answer questions about the current problem. Do not give away the full solution unless asked.


**User prompt:**

Current problem: {problem_summary}
User's code: {user_code}
User question: {user_query}


---

## Response Parsing & Error Handling
- Use `response.text` then `json.loads()`.
- If JSON invalid, retry with `temperature=0.2` or fallback to curated content.
- Strip markdown code fences from JSON strings.

## Caching Strategy
- Cache AI-generated questions per (user_id, topic, difficulty) for 24 hours (Redis).
- Cache feedback for identical (question_id, code_hash) for 7 days.

## Fallback Models
- If Gemini fails (rate limit, timeout), retry with Groq (Llama 3 70B).
- If both fail, serve curated question from database.





