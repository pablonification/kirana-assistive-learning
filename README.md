# Kirana

Kirana is a team-built full-stack assistive learning prototype for Indonesian elementary students, especially children in grades 1-3 who may show early indicators of dyslexia.

It combines handwriting analysis, gaze tracking, oral-reading analysis, adaptive PDF reading, and Indonesian-first AI guidance.

> **Clinical disclaimer:** Kirana provides screening support only. It is not a clinical diagnosis tool and must not replace assessment by qualified professionals.

## Repository context

This repository is a personal fork of [Starath/ADA-SPARTANS-EXTENDED](https://github.com/Starath/ADA-SPARTANS-EXTENDED).

The fork preserves the original team history while documenting the project identity and changes made in this repository.

## Changes in this fork

This fork includes:

- the frontend homepage;
- favicon integration;
- README and landing-page documentation improvements; and
- the project rename from DyslexiAID to Kirana.

Kirana remains a team project. See the original repository and Git history for the complete project context.

## What it demonstrates

- **Multimodal screening support:** YOLO-based handwriting analysis, WebGazer calibration, and Indonesian oral-reading transcription with faster-whisper.
- **Adaptive reading:** PDF extraction, adjustable reading presentation, gaze-aware sessions, and student reading links.
- **AI orchestration:** A multi-step reasoning flow with researcher, diagnostician, critic, and reporter stages.
- **Retrieval:** Indonesian dyslexia guidance, reading-fluency benchmarks, sentence-transformer retrieval, and optional PostgreSQL with pgvector.

## Architecture

```text
Next.js frontend
  landing page, screening flow, Smart Reader, WebGazer, browser media
          |
          | HTTP, multipart, JSON
          v
FastAPI backend
  PDF extraction, transcription, handwriting analysis, diagnosis
          |
          +-- PyMuPDF
          +-- faster-whisper
          +-- YOLO
          +-- LangGraph-style reasoning
          +-- knowledge retrieval
```

## Technology

- **Frontend:** Next.js 15, React 19, TypeScript, Tailwind CSS, WebGazer, Zustand, Vitest, Playwright
- **Backend:** Python 3.11+, FastAPI, Uvicorn, Pydantic, PyMuPDF, faster-whisper, PyTorch, Ultralytics YOLO
- **AI and data:** LangGraph, OpenAI-compatible APIs, sentence-transformers, PostgreSQL, pgvector

## Repository layout

```text
backend/
  app/          API routes, services, models, and reasoning graph
  data/         dyslexia knowledge data
  ingest/       source guidance and reading benchmarks
  tests/        backend tests
frontend/
  src/app/      landing page and product flows
  src/components/ adaptive reader, screening, and gaze components
  tests/        frontend tests
docs/           API contracts
```

## Run locally

Requirements:

- Python 3.11+
- Node.js 20+
- npm
- A browser with camera and microphone permissions for the full screening flow

Run the backend in one terminal:

```bash
cd backend
python -m venv .venv
source .venv/bin/activate
pip install -r requirements-dev.txt
cp .env.example .env
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000 --env-file .env
```

Run the frontend in another terminal:

```bash
cd frontend
npm install
npm run dev
```

Open `http://localhost:3000`. The backend health check is available at `http://localhost:8000/health`.

## Configuration

Create `backend/.env` and `frontend/.env.local`. Use local environment variables for secrets and never commit them.

Backend variables commonly used:

```env
OPENROUTER_API_KEY=your-key
GROQ_API_KEY=your-key
WHISPER_MODEL_SIZE=base
WHISPER_DEVICE=cpu
HANDWRITING_MODEL_PATH=backend/app/models/handwriting/yolo26n.pt
```

Frontend variables commonly used:

```env
NEXT_PUBLIC_BACKEND_URL=http://localhost:8000
GROQ_API_KEY=your-key
OPENROUTER_API_KEY=your-key
PRODUCTION=false
```

Real handwriting inference requires a local YOLO model file. The first Whisper run may download a model and can take longer than later runs.

## Main API routes

| Method | Route | Purpose |
| --- | --- | --- |
| GET | `/health` | Backend health check |
| POST | `/api/pdf/extract` | Extract text and page data from a PDF |
| POST | `/api/pdf/session` | Create a student reading session |
| POST | `/api/audio/transcribe` | Transcribe oral-reading audio |
| POST | `/api/handwriting/analyze` | Analyze a handwriting image |
| POST | `/api/diagnose` | Run the screening reasoning flow |

## Tests

```bash
(cd backend && pytest)
(cd frontend && npm test)
(cd frontend && npm run typecheck)
(cd frontend && npm run build)
```

## Current limitations

- PDF sessions are stored in memory and disappear after a backend restart.
- Camera and microphone permissions are required for the complete screening flow.
- The current setup is a prototype and needs authentication, restricted CORS, upload limits, managed secrets, and durable storage before production use.
- Child-related data such as handwriting, audio, gaze data, and screening indicators requires careful privacy and retention controls.

