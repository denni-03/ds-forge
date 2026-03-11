# AI Question Generation

## Objective

Automatically generate new interview-style problems using datasets.

## Inputs

The AI receives:

- dataset schema
- difficulty level
- problem category

Example:

Dataset: ecommerce_orders

Tables:
orders(order_id, customer_id, order_value)

Difficulty: Medium

## AI Task

The model generates:

1. Question description
2. Expected output
3. Hints
4. Solution query

## Prompt Example

Generate a SQL interview question using the dataset schema below.

Dataset schema:
orders(order_id, customer_id, order_value)

Difficulty:
Medium

Return format:

Question  
Expected Output  
Hints  
Solution

## Validation

Generated problems must be validated by:

- automated tests
- dataset evaluation queries

## Benefits

AI-generated questions allow DS-Forge to scale its problem library quickly.
