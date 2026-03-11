# Database Design

## Users Table

Fields
- user_id
- email
- password_hash
- created_at

## Problems Table

Fields
- problem_id
- title
- description
- difficulty
- dataset_id

## Datasets Table

Fields
- dataset_id
- dataset_name
- schema_definition

## Submissions Table

Fields
- submission_id
- user_id
- problem_id
- code
- result
- execution_time

## Leaderboard Table

Fields
- user_id
- score
