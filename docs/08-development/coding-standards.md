# Coding Standards

## Objective

Maintain consistent and maintainable code across the DS-Forge project.

## General Principles

- code must be readable
- follow consistent naming conventions
- avoid duplication

## Backend Standards

Language: Python

Framework: Django

Guidelines:

- use class-based views
- separate business logic into services
- avoid large view functions

Example structure:

views/
services/
models/
serializers/

## Frontend Standards

Framework: React or Next.js

Guidelines:

- components should be reusable
- separate UI and logic
- use descriptive component names

## Git Practices

Use feature branches:

feature/problem-library  
feature/ai-hints

Commit messages should clearly describe changes.

Example:

Add dataset viewer component
