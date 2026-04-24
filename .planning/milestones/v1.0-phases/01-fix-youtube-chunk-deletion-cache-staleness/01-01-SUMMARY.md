---
phase: 01-fix-youtube-chunk-deletion-cache-staleness
plan: 01
subsystem: study-page
tags: [youtube, rag, backfill, cache-invalidation, firestore, react]

# Dependency graph
requires: []
provides:
  - "Backfill includes YouTube URLs from Firestore sources"
  - "Cache invalidation after YouTube source addition"
affects: [02-verify-end-to-end-rag-retrieval]

# Tech tracking
tech-stack:
  added: []
  patterns:
    - "Fetch Firestore sources directly in backfill to bypass React state race condition"
    - "Mirror handleToggleSource cache reset pattern in handleAddSourceFromUrl"

key-files:
  created: []
  modified:
    - "frontend/src/app/study/[topic]/page.tsx"

key-decisions:
  - "Fetched Firestore sources directly inside backfillSources rather than relying on React state — avoids race condition with loadSources"
  - "Replicated full cache/state reset pattern from handleToggleSource (not just sourceTruthVersion increment) to ensure complete regeneration"

patterns-established:
  - "Direct Firestore reads in effects when React state may not yet be populated"
  - "Full cache/state reset on any source mutation: sourceTruthVersion + chatHistory + quickActions + learnedItems + microQuizzes + examMode + expandedOriginalQuestions"

requirements-completed: [BKFL-01, BKFL-02, BKFL-03, CACH-01, CACH-02]

# Metrics
duration: 3min
completed: 2026-03-02
---

# Phase 1 Plan 1: Fix Backfill YouTube URL Inclusion & Cache Invalidation Summary

**Patched backfillSources to fetch YouTube URLs from Firestore before replaceIndexedChunks, and added sourceTruthVersion increment with full cache reset after handleAddSourceFromUrl success**

## Performance

- **Duration:** 3 min
- **Started:** 2026-03-01T23:07:15Z
- **Completed:** 2026-03-01T23:10:30Z
- **Tasks:** 2
- **Files modified:** 1

## Accomplishments
- Backfill now fetches existing YouTube source records from Firestore and passes their URLs to /api/sources/index, preventing replaceIndexedChunks from deleting YouTube transcript chunks on page reload
- handleAddSourceFromUrl now increments sourceTruthVersion and clears all cached study state after successful source addition, forcing regeneration of study content that includes YouTube context

## Task Commits

Each task was committed atomically:

1. **Task 1: Fix backfill to include YouTube URLs from Firestore sources** - `5c91421` (fix)
2. **Task 2: Add cache invalidation after YouTube source addition** - `380adfb` (fix)

## Files Created/Modified
- `frontend/src/app/study/[topic]/page.tsx` - Fixed backfillSources to include YouTube URLs and added cache invalidation in handleAddSourceFromUrl

## Decisions Made
- Fetched Firestore sources directly inside backfillSources rather than relying on React state — the React state `sources` starts as `[]` and only populates after `loadSources` finishes, causing a race condition
- Replicated the full cache/state reset pattern from handleToggleSource (lines 990-1002) in handleAddSourceFromUrl, not just the sourceTruthVersion increment, to ensure chatHistory, quickActions, learnedItems, microQuizzes, examMode, and expandedOriginalQuestions are all cleared

## Deviations from Plan

None - plan executed exactly as written.

## Issues Encountered
None

## User Setup Required
None - no external service configuration required.

## Next Phase Readiness
- Phase 1 complete, ready for Phase 2: Verify End-to-End RAG Retrieval
- Both root cause fixes are in place; Phase 2 should verify that YouTube chunks survive page loads and appear in generated study content

---
*Phase: 01-fix-youtube-chunk-deletion-cache-staleness*
*Completed: 2026-03-02*

## Self-Check: PASSED
