# Deployment Strategy

## Overview
DS-Forge is deployed using **containerized services** on free-tier platforms: **Render (backend)** and **Vercel (frontend)**.

## Infrastructure Components

| Component | Technology | Hosting |
|-----------|------------|---------|
| Frontend | React + Vite | Vercel (free) |
| Backend API | Django + DRF | Render (free) |
| Database | PostgreSQL (Neon) | Neon (free 1GB) |
| Cache/Queue | Redis (Upstash) | Upstash (free) |
| File Storage | Cloudinary | Cloudinary (free 25GB) |
| AI Service | Gemini API | Google (free 60 req/min) |
| Code Execution | Piston API | External (1000 req/day free) |

## Containerization (Docker)

### Dockerfile for Backend
```dockerfile
FROM python:3.11-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
CMD ["gunicorn", "dsforge_backend.wsgi:application", "--bind", "0.0.0.0:8000"]
**
Docker Compose for Local Dev**

version: '3.8'
services:
  db:
    image: postgres:15
    environment:
      POSTGRES_DB: dsforge
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
  redis:
    image: redis:7
  backend:
    build: ./backend
    ports:
      - "8000:8000"
    depends_on:
      - db
      - redis




