# KalExam

> AI-Powered Exam Preparation — Intelligent study tracking, adaptive learning pathways, and real-time readiness scoring.

[![Next.js](https://img.shields.io/badge/Next.js-16-black?style=flat-square&logo=next.js)](https://nextjs.org)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.0-blue?style=flat-square&logo=typescript)](https://www.typescriptlang.org)
[![Firebase](https://img.shields.io/badge/Firebase-10+-orange?style=flat-square&logo=firebase)](https://firebase.google.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=flat-square)](./LICENSE)

---

## Overview

KalExam transforms how students prepare for exams by combining intelligent content analysis with real-time progress intelligence. Upload study materials (PDFs, Word docs, PowerPoints), get an AI-generated strategy, and track your readiness with live scoring.

### Key Features

- **AI Strategy Generation** — Upload syllabus and study materials → get prioritized learning pathways
- **Exam Mode Readiness** — Real-time scoring (0–100) with likely questions and weak area identification
- **Smart Chat Learning** — Contextual Q&A with cached responses to reduce AI costs
- **Live Progress Tracking** — Per-topic status, time spent, and confidence scoring
- **Adaptive Recommendations** — Algorithm recommends next topic based on exam likelihood, priority, and time
- **PDF Report Export** — Download multi-page progress reports

---

## Screenshots

<div align="center">

### Landing Page
![Landing Page](./docs/screenshots/landing.png)

### Dashboard
![Dashboard](./docs/screenshots/dashboard.png)

### Study Interface
![Study Interface](./docs/screenshots/study-interface.png)

</div>

> **Note:** Replace these placeholder images with actual screenshots from the deployed application.

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Frontend** | Next.js 16, TypeScript, TailwindCSS, shadcn/ui |
| **Backend** | Next.js API Routes, Node.js |
| **Database** | Firestore (real-time progress, strategies, sessions) |
| **Auth** | Firebase Authentication (email/password, OAuth) |
| **AI** | Gemini, OpenAI, RAG pipeline |
| **File Parsing** | pdf-parse, docx-parser, pptx-parser |
| **PDF Export** | jsPDF |

---

## Architecture Highlights

### Async Job Pipeline
Strategy generation runs as a multi-stage pipeline with immediate return and long-polling:
- **Stages**: Queued → Extracting → Analyzing → Generating → Preparing → Complete
- Client polls status while precomputing recommended topics

### Session-Level Intelligence
- `TopicProgress` model tracks learning status, time spent, and confidence per topic
- Auto-marks topics as "learning" on first open, "completed" on finish
- Session caching prevents redundant LLM calls for identical queries

### Recommendation Algorithm
Six-factor scoring for next-topic suggestions:
- Exam likelihood (0–100) + Chapter weightage (0–100)
- Unfinished bonus + Priority score + Time remaining factor

### Exam Readiness (0–100)
- Generates 3 likely questions from weak areas
- Adjusts score based on retrieval confidence
- Per-topic caching with model signature invalidation

---

## Getting Started

### Prerequisites

- Node.js 18+
- Firebase project with Firestore enabled
- API keys for LLM providers (Gemini, OpenAI)

### Installation

```bash
# Clone the repository
git clone https://github.com/nihar5hah/kalexam.git
cd kalexam

# Install dependencies
npm install

# Configure environment variables
cp .env.example .env.local
```

### Environment Variables

```
NEXT_PUBLIC_FIREBASE_API_KEY=...
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN=...
NEXT_PUBLIC_FIREBASE_PROJECT_ID=...
FIREBASE_SERVICE_ACCOUNT_KEY=...
```

### Run Development Server

```bash
npm run dev
# Open http://localhost:3000
```

---

## Project Structure

```
src/
├── app/                    # Next.js App Router
│   ├── api/               # API routes
│   │   ├── generate-strategy/   # AI strategy generation
│   │   └── study/              # Study & exam mode APIs
│   ├── auth/              # Authentication page
│   ├── dashboard/         # User dashboard
│   ├── study/[topic]/    # Topic study interface
│   └── upload/           # File upload page
├── components/           # React components
│   ├── ui/               # shadcn/ui components
│   └── study/            # Study-specific components
└── lib/                  # Core utilities
    ├── ai/               # AI client & providers
    ├── firestore/       # Database operations
    ├── parsing/         # File parsing (PDF, DOCX, PPTX)
    └── study/           # Study logic & RAG
```

---

## API Endpoints

### Generate Strategy

```bash
POST /api/generate-strategy
{
  "syllabusFiles": [...],
  "syllabusTextInput": "...",
  "studyMaterialFiles": [...]
}
```

### Study Chat

```bash
POST /api/study/ask
{
  "topic": "Organic Chemistry",
  "question": "What is a benzene ring?",
  "strategyId": "..."
}
```

### Exam Mode

```bash
POST /api/study/exam-mode
{
  "topic": "Organic Chemistry",
  "files": ["..."]
}
# Returns: { readinessScore, likelyQuestions, weakAreas, examTip }
```

---

## Database Schema

### Firestore Collections

- **`strategies/{strategyId}`** — AI-generated study strategies
- **`users/{uid}/studySessions/{sessionId}`** — Per-session progress tracking
- **`sources/{sourceId}`** — Uploaded study materials

---

## Roadmap

- [ ] Production: Migrate to Cloud Tasks for job queue
- [ ] Dashboard analytics with exam countdown
- [ ] Spaced repetition scheduling
- [ ] React Native mobile app

---

## License

This project is licensed under the [MIT License](./LICENSE).

---

## Author

**Nihar Shah** — Full-stack AI Engineer  
GitHub: [@nihar5hah](https://github.com/nihar5hah)

---

Built with ❤️ for students everywhere.
