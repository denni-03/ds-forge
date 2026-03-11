# System Architecture

## Overview

The DS-Forge system consists of three main layers:

Frontend
Backend
AI services

## Frontend

Responsibilities:

- user interface
- problem display
- code editor
- result visualization

Technology:

React or Next.js

## Backend

Responsibilities:

- authentication
- problem management
- submission handling
- evaluation engine

Technology:

Django REST API

## Database

Stores:

- users
- problems
- datasets
- submissions

Technology:

PostgreSQL

## Code Execution Service

Executes user code inside isolated environments.

Implementation:

Docker containers.

## AI Services

Responsible for:

- generating questions
- providing hints
- explaining solutions

Models may include open-source LLMs.
