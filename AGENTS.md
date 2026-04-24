# AGENTS.md

Guidance for agentic coding tools working in this repository.

---

## Project Overview

KalExam is an AI-powered exam study strategy tool. It parses uploaded study materials (PDF, PPTX, DOCX, YouTube, URLs), generates exam strategies via LLMs, and presents interactive study sessions.

**Stack:** Next.js 15 (App Router) + React 19 + TypeScript 5 + Tailwind CSS v4 + Firebase (Firestore, Auth, Hosting) + shadcn/ui (new-york style).

**Structure:**
```
frontend/   ← Next.js app (primary codebase)
backend/    ← Minimal Express stub (health check only, mostly unused)
firestore.rules / storage.rules ← Firebase security rules
```

---

## Build, Lint & Dev Commands

All commands are run from the respective subdirectory, not the repo root.

### Frontend (`frontend/`)
```bash
npm run dev        # Start Next.js dev server (Turbopack)
npm run build      # Production build
npm run start      # Serve production build
npm run lint       # ESLint (eslint-config-next/core-web-vitals + typescript)
```

### Backend (`backend/`)
```bash
npm run dev        # nodemon src/index.ts (ts-node)
npm run build      # tsc → dist/
npm run start      # node dist/index.js
```

### No Test Suite
There are **no tests** in this codebase. `backend/package.json` has `"test": "echo \"Error: no test specified\" && exit 1"`. Do not add test placeholders; if adding tests, use Jest with ts-jest for TypeScript.

### Type Checking (without building)
```bash
# frontend/
npx tsc --noEmit

# backend/
npx tsc --noEmit
```

---

## TypeScript Configuration

### Frontend (`frontend/tsconfig.json`)
- `target: ES2017`, `module: esnext`, `moduleResolution: bundler`
- `strict: true`, `noEmit: true`
- Path alias: `@/*` → `./src/*` — always use `@/` for imports within `frontend/src/`

### Backend (`backend/tsconfig.json`)
- `target: es2020`, `module: commonjs`
- `strict: true`, `rootDir: ./src`, `outDir: ./dist`

---

## Code Style

### Formatting
- **Frontend:** 2-space indentation, double quotes for strings.
- **Backend:** 4-space indentation, single quotes for strings.
- No Prettier config is present; rely on ESLint (`eslint-config-next`) for the frontend.
- Trailing commas on multi-line argument lists and object literals (ES2017+ style).

### Imports
- **Third-party imports first**, then a blank line, then **internal `@/` imports**.
- Multi-named imports from the same module are each on their own line when there are more than ~3.
- Use `export type` / `import type` for type-only exports and imports.

```ts
// Correct
import {
  collection,
  deleteDoc,
  doc,
  getDocs,
} from "firebase/firestore";

import { getFirebaseDb } from "@/lib/firebase";
```

- Never use relative `../` paths inside `frontend/src/`; always use the `@/` alias.
- Backend uses relative paths since there is no alias configured there.

### Types
- Prefer **`type` aliases** over `interface` throughout.
- Use **union string literals** for enums (e.g., `"high" | "medium" | "low"`), not TypeScript `enum`.
- Explicit return types on exported functions.
- Use `unknown` instead of `any`; cast with `as` only when the shape is verified.
- Optional fields use `?` on the type property; runtime defaults use `?? fallback`.

```ts
// Correct
export type StudySourceStatus = "processing" | "indexed" | "error";

export type StudySourceRecord = {
  id: string;
  status: StudySourceStatus;
  errorMessage?: string;
};
```

### Naming Conventions
| Construct | Convention | Example |
|---|---|---|
| Files (components) | PascalCase | `AuthProvider.tsx` |
| Files (lib/utils) | camelCase | `ai-client.ts`, `modelRouter.ts` |
| React components | PascalCase | `AuthProvider`, `FileUploadGroup` |
| Functions & hooks | camelCase | `upsertStudySource`, `useAuth` |
| Custom hooks | `use` prefix | `useAuth`, `useCallback` |
| Type aliases | PascalCase | `StudySourceRecord`, `ModelConfig` |
| Constants | UPPER_SNAKE or camelCase | `BATCH_LIMIT`, `PORT` |
| Firestore collections | camelCase strings | `"indexedChunks"`, `"strategies"` |

### Async / Promises
- **Always `async/await`** — no `.then()/.catch()` chains.
- Fire-and-forget calls use the `void` operator: `void load();`
- Wrap unpredictable async in try/catch; use empty `catch { }` (no variable) when the error is intentionally swallowed.

```ts
// Correct
async function fetchSources(uid: string): Promise<StudySourceRecord[]> {
  try {
    return await listStudySources(uid, strategyId);
  } catch {
    return [];
  }
}
```

### React Patterns
- All client components start with `"use client";` at the top.
- Server components (default in App Router) do **not** have that directive.
- Use `useMemo` and `useCallback` to memoize derived values and event handlers in components that re-render frequently.
- Context consumers export both the Provider and a typed `useXxx()` hook that throws if used outside the provider.
- Guard clauses at the top of event handlers and effects: `if (!user || !id) return;`

```tsx
// Correct
const value = useMemo(() => ({ user, loading }), [user, loading]);
```

### Prompt Building
LLM prompts are built as arrays of strings joined with `"\n"`:

```ts
return [
  "Create an exam study strategy...",
  `Hours left before exam: ${input.hoursLeft}`,
  `Syllabus:\n${syllabusSnippet}`,
].join("\n");
```

### Firestore Conventions
- Batched writes use `writeBatch` with a `BATCH_LIMIT = 400` constant (below Firestore's 500 limit).
- Document paths follow the pattern: `users/{uid}/strategies/{strategyId}/sources/{sourceId}`.
- Always include `updatedAt: serverTimestamp()` on writes; include `createdAt` on creates (with `{ merge: true }`).
- Use `Omit<Record, "id">` when spreading `item.data()` to reconstruct typed records from snapshots.

### Error Handling
- Throw `Error` objects with descriptive messages; never throw raw strings.
- In React components, errors that affect the UI should set state (e.g., `setError(msg)`) rather than re-throwing.
- API routes return `{ error: string }` JSON with an appropriate HTTP status code on failure.
- The `useAuth()` hook throws immediately if called outside `AuthProvider` — this is intentional and catches provider misconfiguration at development time.

---

## Firebase & Auth
- Client-side Firebase is initialized lazily in `@/lib/firebase.ts` via `getFirebaseDb()` / `getFirebaseAuth()`.
- Admin SDK (server-side) is in `@/lib/firebase-admin.ts` and only used in Next.js API routes (`app/api/**`).
- All API routes require a Bearer token: `Authorization: Bearer <idToken>` obtained from `user.getIdToken()`.
- Never import `firebase-admin` from a client component.

---

## shadcn/ui & Tailwind
- Component library: shadcn/ui **new-york** style, located in `@/components/ui/`.
- Add new shadcn components with: `npx shadcn@latest add <component>` (run from `frontend/`).
- Use the `cn()` utility from `@/lib/utils` for conditional class merging (`clsx` + `tailwind-merge`).
- Tailwind CSS v4 — no `tailwind.config.js`; configuration is in `src/app/globals.css` via `@theme`.
- Icon library: `lucide-react` (tree-shaken via `optimizePackageImports` in `next.config.ts`).

---

## File Organization
```
frontend/src/
  app/              ← Next.js App Router pages and API routes
  components/       ← Shared UI components
    ui/             ← shadcn/ui primitives (do not edit manually)
  lib/
    ai/             ← LLM client, model router, type definitions
    firebase.ts     ← Client Firebase init
    firebase-admin.ts ← Server Firebase init
    firestore/      ← Typed Firestore CRUD helpers (one file per collection)
    parsing/        ← File parsers (PDF, DOCX, PPTX, YouTube, URL)
    study/          ← RAG, precompute, exam-likelihood logic
    utils.ts        ← cn() helper
  types/            ← Global ambient type declarations
```

New Firestore collections get their own file in `lib/firestore/`. New AI capabilities go in `lib/ai/`. Parsing logic for a new file type goes in `lib/parsing/`.
