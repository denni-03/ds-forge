# Evaluation Engine

## Purpose
The evaluation engine checks whether a user’s solution produces the correct result.

## Process

1. User submits code
2. Code is executed in sandbox
3. Output is captured
4. Output compared with expected answer

## Example Evaluation

Expected result

customer_id | revenue
1 | 500
2 | 300

User query output is compared row-by-row.

## Challenges

- handling floating point errors
- ordering of results
- large datasets

## Security

User code runs in isolated containers to prevent system access.
