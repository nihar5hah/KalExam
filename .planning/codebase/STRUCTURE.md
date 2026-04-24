# Codebase Structure

**Analysis Date:** 2026-03-02

## Directory Layout

```
KalExam/
├── .planning/                    # Planning documents
├── backend/                      # Separate Express backend (minimal)
│   ├── src/
│   │   └── index.ts             # Express entry point
│   ├── package.json
│   └── tsconfig.json
├── frontend/                     # Main application (Next.js)
│   ├── src/
│   │   ├── app/                 # Next.js App Router pages
│   │   ├── components/          # React components
│   │   ├── lib/                 # Libraries and utilities
│   │   └── types/               # TypeScript type definitions
│   ├── public/                  # Static assets
│   ├── package.json
│   └── tsconfig.json
├── firebase.json                 # Firebase configuration
├── firestore.indexes.json        # Firestore indexes
├── firestore.rules              # Firestore security rules
└── storage.rules                # Firebase Storage rules
```

## Directory Purposes

### Frontend `src/app/` - Next.js Pages
- **Purpose:** Next.js App Router pages and API routes
- **Contains:** Page components, layouts, API route handlers
- **Key files:**
  - `frontend/src/app/layout.tsx` - Root layout with AuthProvider
  - `frontend/src/app/page.tsx` - Landing page
  - `frontend/src/app/dashboard/page.tsx` - User dashboard
  - `frontend/src/app/upload/page.tsx` - File upload
  - `frontend/src/app/strategy/page.tsx` - Strategy view
  - `frontend/src/app/study/[topic]/page.tsx` - Study session
  - `frontend/src/app/api/*/route.ts` - API endpoints

### Frontend `src/components/` - React Components
- **Purpose:** Reusable UI components
- **Contains:** Feature components, UI components, hooks
- **Subdirectories:**
  - `components/ui/` - shadcn/ui components (button, card, badge, etc.)
  - `components/study/` - Study-specific components (StudyChatPanel, StudyTopicHero)
  - `components/dashboard/` - Dashboard components (StrategyList)
  - `components/hooks/` - Custom React hooks

### Frontend `src/lib/` - Core Libraries
- **Purpose:** Business logic, data access, utilities
- **Contains:** AI, RAG, Firestore, parsing, utilities
- **Key subdirectories:**
  - `lib/ai/` - AI client, model router, types
  - `lib/study/` - RAG pipeline, study content generation
  - `lib/firestore/` - Firestore data access (strategies, sources, chunks)
  - `lib/parsing/` - File parsing (PDF, DOCX, PPT, URL)
  - `lib/server/` - Server-side utilities (auth)

### Frontend `src/types/` - Type Definitions
- **Purpose:** Global TypeScript types
- **Contains:** Type declarations
- **Key files:** `types/pdf-parse.d.ts`

### Backend - Standalone Express
- **Purpose:** Separate backend for potential Firebase Functions
- **Contains:** Express server setup
- **Key files:** `backend/src/index.ts`

## Key File Locations

### Entry Points
- `frontend/src/app/layout.tsx`: Root layout with AuthProvider
- `frontend/src/app/page.tsx`: Landing page
- `backend/src/index.ts`: Standalone Express server

### Configuration
- `frontend/package.json`: Frontend dependencies
- `backend/package.json`: Backend dependencies
- `frontend/tsconfig.json`: TypeScript config with path aliases (@/*)
- `firebase.json`: Firebase project config
- `firestore.rules`: Firestore security rules
- `frontend/.env.local`: Frontend environment variables

### Core Logic
- `frontend/src/lib/ai/ai-client.ts`: Strategy generation
- `frontend/src/lib/ai/modelRouter.ts`: Model routing
- `frontend/src/lib/study/rag.ts`: RAG pipeline
- `frontend/src/lib/parsing/index.ts`: Parsing orchestration

### Data Access
- `frontend/src/lib/firebase.ts`: Firebase client initialization
- `frontend/src/lib/firestore/strategies.ts`: Strategy CRUD
- `frontend/src/lib/firestore/sources.ts`: Source management
- `frontend/src/lib/firestore/chunks.ts`: Chunk indexing

### Authentication
- `frontend/src/components/AuthProvider.tsx`: Client auth provider
- `frontend/src/lib/server/auth.ts`: Server auth utilities
- `frontend/src/components/RequireAuth.tsx`: Auth guard component

## Naming Conventions

### Files
- **Components:** PascalCase with descriptive names (`StudyChatPanel.tsx`, `StrategyReport.tsx`)
- **Utilities:** kebab-case (`parse-pdf.ts`, `model-router.ts`)
- **Types:** PascalCase (`types.ts`, `StrategyResult`)
- **Route handlers:** `route.ts` (Next.js convention)

### Directories
- **Components:** PascalCase for feature folders (`study/`, `dashboard/`), lowercase for ui (`ui/`)
- **Lib:** Lowercase functional groups (`ai/`, `firestore/`, `parsing/`, `study/`)
- **API:** RESTful nested paths (`study/topic/`, `generate-strategy/`)

### Variables & Functions
- **Functions:** camelCase (`buildTopicStudyContent`, `generateStrategy`)
- **Types/Interfaces:** PascalCase (`StrategyResult`, `UploadedFile`)
- **Constants:** SCREAMING_SNAKE_CASE (`STOP_WORDS`, `FAST_MODEL`)

## Where to Add New Code

### New Feature
- **Page:** `frontend/src/app/[feature]/page.tsx`
- **API:** `frontend/src/app/api/[feature]/route.ts`
- **Components:** `frontend/src/components/[feature]/`
- **Data access:** `frontend/src/lib/firestore/[feature].ts`
- **Tests:** Not detected

### New Component/Module
- **UI Component:** `frontend/src/components/ui/[component-name].tsx`
- **Feature Component:** `frontend/src/components/[feature]/[ComponentName].tsx`
- **Shared Utility:** `frontend/src/lib/[category]/[utility-name].ts`

### New API Endpoint
- **Route Handler:** `frontend/src/app/api/[domain]/[endpoint]/route.ts`
- **Types:** Add to `frontend/src/lib/ai/types.ts` if related to AI
- **Handler Logic:** Create new file in `frontend/src/lib/` or reuse existing

### Utilities
- **Shared helpers:** `frontend/src/lib/utils.ts`
- **AI utilities:** `frontend/src/lib/ai/`
- **Study utilities:** `frontend/src/lib/study/`

## Special Directories

### `frontend/src/app/api/`
- **Purpose:** Next.js API route handlers
- **Generated:** No
- **Committed:** Yes

### `frontend/.next/`
- **Purpose:** Next.js build output
- **Generated:** Yes (on build)
- **Committed:** No (in .gitignore)

### `frontend/node_modules/`
- **Purpose:** npm dependencies
- **Generated:** Yes (on npm install)
- **Committed:** No

### `backend/node_modules/`
- **Purpose:** Backend dependencies
- **Generated:** Yes
- **Committed:** No

### `frontend/public/`
- **Purpose:** Static assets (images, fonts)
- **Generated:** No
- **Committed:** Yes

### `.firebase/`
- **Purpose:** Firebase hosting cache
- **Generated:** Yes (on Firebase operations)
- **Committed:** No

---

*Structure analysis: 2026-03-02*
