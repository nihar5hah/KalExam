# Architecture

**Analysis Date:** 2026-03-02

## Pattern Overview

**Overall:** Serverless-First Multi-Tier with RAG Architecture

**Key Characteristics:**
- Frontend: Next.js 14+ with App Router for React server components
- Backend: Express.js serverless functions within Next.js API routes + separate Express backend for Firebase Functions
- Database: Firebase Firestore for user data, strategies, and study progress
- Storage: Firebase Storage for uploaded study materials (PDFs, DOCX, PPT)
- AI: Multi-model AI routing (Gemini + custom providers) with RAG pipeline for study content generation

## Layers

### Frontend Layer (Next.js App)
- **Location:** `frontend/src/app/`
- **Contains:** Pages, API routes, layouts
- **Depends on:** React, Next.js, shadcn/ui components, Firebase client SDK
- **Used by:** Browser clients

**Key Routes:**
- `/` - Landing page with hero and authentication
- `/auth` - Authentication page
- `/dashboard` - User dashboard with strategy list
- `/upload` - File upload and strategy generation
- `/strategy` - Strategy viewing and editing
- `/study/[topic]` - Topic study session
- `/settings` - User settings

### API Layer (Next.js API Routes)
- **Location:** `frontend/src/app/api/`
- **Contains:** Route handlers for study, sources, strategy generation
- **Depends on:** AI libraries, Firestore, Firebase Storage
- **Used by:** Frontend pages

**Key API Routes:**
- `/api/study/topic` - Generate study content for a topic
- `/api/study/learn-item` - Generate detailed learning item content
- `/api/study/micro-quiz` - Generate quiz questions
- `/api/study/exam-mode` - Generate exam simulation
- `/api/study/ask` - Chat follow-up questions
- `/api/sources/index` - List user sources
- `/api/generate-strategy` - Generate study strategy from uploaded files

### AI & RAG Layer
- **Location:** `frontend/src/lib/ai/`, `frontend/src/lib/study/`
- **Contains:** AI client, model router, RAG pipeline, study content generation
- **Depends on:** Gemini API, custom AI providers, Firestore chunks
- **Used by:** API routes, frontend components

**Key Files:**
- `frontend/src/lib/ai/ai-client.ts` - Strategy generation client
- `frontend/src/lib/ai/modelRouter.ts` - Multi-model routing
- `frontend/src/lib/study/rag.ts` - RAG pipeline with chunk retrieval and content generation

### Data Layer (Firestore)
- **Location:** `frontend/src/lib/firestore/`
- **Contains:** Firestore data access for strategies, sources, chunks, study sessions
- **Depends on:** Firebase Firestore SDK
- **Used by:** API routes, frontend components

**Key Files:**
- `frontend/src/lib/firestore/strategies.ts` - Strategy CRUD
- `frontend/src/lib/firestore/sources.ts` - Source document management
- `frontend/src/lib/firestore/chunks.ts` - Chunk indexing and retrieval
- `frontend/src/lib/firestore/chunks-admin.ts` - Admin chunk operations

### Parsing Layer
- **Location:** `frontend/src/lib/parsing/`
- **Contains:** File parsing for PDF, DOCX, PPT, URL content extraction
- **Depends on:** pdf-parse, mammoth, specialized parsers
- **Used by:** Upload flow, RAG pipeline

**Key Files:**
- `frontend/src/lib/parsing/parse-pdf.ts`
- `frontend/src/lib/parsing/parse-docx.ts`
- `frontend/src/lib/parsing/parse-ppt.ts`
- `frontend/src/lib/parsing/chunker.ts` - Text chunking for RAG

### Backend Layer (Separate Express)
- **Location:** `backend/src/`
- **Contains:** Express server for standalone operations
- **Depends on:** Express, CORS, dotenv
- **Used by:** Standalone deployment (not used in main flow)

**Entry Point:** `backend/src/index.ts`

### Authentication Layer
- **Location:** `frontend/src/components/AuthProvider.tsx`, `frontend/src/lib/server/auth.ts`
- **Contains:** Firebase Auth integration, session management
- **Depends on:** Firebase Auth
- **Used by:** All protected routes

## Data Flow

### Strategy Generation Flow:
1. User uploads files (syllabus, study material, previous papers) via `/upload`
2. Files are parsed (`parsing/parse-*.ts`) to extract text
3. Text is chunked (`parsing/chunker.ts`) for RAG
4. Chunks are indexed in Firestore (`firestore/chunks.ts`)
5. AI generates strategy (`ai/ai-client.ts`) using extracted texts
6. Strategy is saved to Firestore (`firestore/strategies.ts`)
7. User is redirected to strategy view

### Study Session Flow:
1. User selects topic from strategy
2. `/api/study/topic` is called with topic and file references
3. RAG retrieves relevant chunks (`study/rag.ts`)
4. AI generates study content (explanation, examples, exam tips)
5. Content is cached in Firestore (`studyCache`)
6. User can ask follow-up questions via `/api/study/ask`

### File Upload Flow:
1. User selects files via `UploadForm.tsx`
2. Files uploaded to Firebase Storage
3. Files parsed and chunked
4. Chunks indexed in Firestore
5. Sources document created

## Key Abstractions

### Strategy Abstraction
- **Purpose:** Represents a complete study plan
- **Examples:** `frontend/src/lib/ai/types.ts` - StrategyResult, StudyChapter, StudyTopic
- **Pattern:** TypeScript interfaces with normalization functions

### Source Abstraction
- **Purpose:** Represents uploaded study material
- **Examples:** `frontend/src/lib/ai/types.ts` - UploadedFile, FileCategory
- **Pattern:** Typed with category (syllabus, studyMaterial, previousPapers)

### RAG Context
- **Purpose:** Retrieved context for AI generation
- **Examples:** `frontend/src/lib/study/rag.ts` - ContextChunk, ScoredContextChunk
- **Pattern:** Scoring and diversity enforcement for chunk selection

### Model Router
- **Purpose:** Route AI requests to appropriate model
- **Examples:** `frontend/src/lib/ai/modelRouter.ts`
- **Pattern:** Factory pattern with task-type routing

## Entry Points

### Frontend Entry
- **Location:** `frontend/src/app/layout.tsx`
- **Triggers:** Browser loads application
- **Responsibilities:** Root layout, AuthProvider setup, toaster configuration

### API Entry
- **Location:** `frontend/src/app/api/*/route.ts`
- **Triggers:** Frontend fetch calls
- **Responsibilities:** Request validation, authentication, response formatting

### Study Topic Entry
- **Location:** `frontend/src/app/study/[topic]/page.tsx`
- **Triggers:** User navigates to study topic
- **Responsibilities:** Load topic content, display study interface, handle chat

## Error Handling

**Strategy:**
- Try-catch blocks in API routes with proper error types
- Fallback content generation in RAG pipeline
- Auth error classes (`RequestAuthError`)

**Patterns:**
- API routes return `NextResponse.json({ error }, { status })`
- RAG has fallback messages for empty retrieval
- Auth middleware validates tokens and throws `RequestAuthError`

## Cross-Cutting Concerns

**Logging:** Console logging with contextual prefixes (e.g., `[RAG]`, `[Strategy]`)

**Validation:**
- Request body validation in API routes
- TypeScript type guards
- Environment variable checks

**Authentication:**
- Firebase Auth with Google provider
- Server-side auth verification in API routes
- Client-side auth state via AuthProvider

**Environment Configuration:**
- Frontend: `.env.local` with NEXT_PUBLIC_* prefix
- Backend: `.env` with dotenv

---

*Architecture analysis: 2026-03-02*
