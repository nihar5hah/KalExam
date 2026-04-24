# KalExam - YouTube RAG Bug Fix

## What This Is

KalExam is an AI-powered exam preparation app that generates personalized study strategies from uploaded materials (PDFs, DOCX, PPT, YouTube videos, URLs). It uses RAG (Retrieval Augmented Generation) to ground LLM-generated study content in the user's actual source materials. YouTube transcript chunks now correctly survive page loads and are retrieved by the RAG pipeline, so generated study content is grounded in video material.

## Core Value

YouTube transcript chunks must survive page loads and be retrievable by the RAG pipeline so that LLM-generated study content is grounded in video source material — not just files.

## Requirements

### Validated

- Existing file upload and parsing (PDF, DOCX, PPT) — existing
- Strategy generation from uploaded materials — existing
- RAG pipeline with chunk retrieval, scoring, and diversity enforcement — existing
- Study content generation (topic, learn-item, micro-quiz, exam-mode, ask) — existing
- Firebase Auth with Google provider — existing
- Source management UI (add, toggle, view chunk counts) — existing
- YouTube transcript ingestion (fetch, translate, chunk) — existing
- Chunk indexing in Firestore with versioning — existing
- Study content caching with `sourceTruthVersion` invalidation — existing
- ✓ Backfill fetches YouTube URLs from Firestore source records before calling `replaceIndexedChunks` — v1.0
- ✓ `replaceIndexedChunks` receives YouTube chunks alongside file chunks when backfill triggers — v1.0
- ✓ YouTube chunk documents in Firestore survive page reload — v1.0
- ✓ `sourceTruthVersion` incremented after `handleAddSourceFromUrl` adds a YouTube source — v1.0
- ✓ Cached study content regenerated with YouTube context after adding a YouTube source — v1.0
- ✓ RAG `getTopChunks` returns YouTube chunks when YouTube sources exist — v1.0
- ✓ Generated study content references YouTube video material when YouTube sources are present — v1.0

### Active

(None — v1.0 milestone complete. Define next milestone with `/gsd-new-milestone`.)

### Out of Scope

- Refactoring the overall RAG pipeline — bug fix only, preserve existing behavior
- Adding new source types — out of scope for this fix
- UI/UX redesign of source management — only fix what's broken
- Performance optimization of chunk retrieval — not the goal here
- Test infrastructure setup — no test framework exists yet, adding one is separate work
- Race condition between `loadSources` and `backfillSources` (structural fix) — deferred to v1.1; current Firestore-direct read is correct
- URL source type backfill inclusion — unconfirmed bug, deferred to v1.1

## Context

### Shipped in v1.0

Fixed two root causes in `frontend/src/app/study/[topic]/page.tsx` (1 file, ~20 lines changed):

1. **Backfill race condition**: `backfillSources` now fetches YouTube source records directly from Firestore instead of relying on the `sources` React state array, which starts as `[]` and may not be populated yet when the effect fires.

2. **Cache staleness after source add**: `handleAddSourceFromUrl` now mirrors the full cache/state reset pattern from `handleToggleSource` — increments `sourceTruthVersion` and clears `chatHistory`, `quickActions`, `learnedItems`, `microQuizzes`, `examMode`, and `expandedOriginalQuestions`.

### Key Architecture Details (for future reference)

- `replaceIndexedChunks` (chunks.ts): increments version, writes new chunks, deletes ALL old version chunks
- `appendIndexedChunks` (chunks.ts): writes new chunks with current version, does NOT delete anything
- `getTopChunks` (rag.ts): retrieves chunks from Firestore, applies YouTube-specific boosts (1.35x multiplier, floor of 3)
- YouTube chunks use `sourceType: "Study Material"`, section: `"Transcript N"`

## Constraints

- **Tech Stack**: Must use existing Next.js 16 / React 19 / Firebase / TypeScript stack — no new dependencies
- **No Tests**: No test framework exists; fix verified manually via console logging
- **Backwards Compatibility**: Fix must not break existing file-based RAG workflow
- **Minimal Surface Area**: Change as few files as possible to reduce risk of regression

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Fix backfill to include YouTube URLs | Backfill was passing empty YouTube array, causing chunk deletion | ✓ Good — resolves root cause 1 |
| Add full cache/state reset after YouTube add | Cache must be invalidated when new source material is added | ✓ Good — resolves root cause 2 |
| Preserve `replaceIndexedChunks` / `appendIndexedChunks` API | These are used elsewhere; changing their contract is risky | ✓ Good — no regressions |
| Fetch Firestore sources directly in backfill (not React state) | React state `sources` starts as `[]` due to async `loadSources` | ✓ Good — eliminates race condition |
| Mirror full cache/state reset from `handleToggleSource` | Incrementing only `sourceTruthVersion` leaves stale chat/quiz/exam state | ✓ Good — complete regeneration |

---
*Last updated: 2026-03-03 after v1.0 milestone*
