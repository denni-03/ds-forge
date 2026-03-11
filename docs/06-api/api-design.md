# API Design

## Authentication

POST /api/auth/register  
POST /api/auth/login  

## Problems

GET /api/problems  
GET /api/problems/{id}

## Submissions

POST /api/submissions

Payload:

{
 "problem_id": 1,
 "code": "SELECT * FROM orders"
}

## Leaderboard

GET /api/leaderboard
