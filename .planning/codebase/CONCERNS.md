# Codebase Concerns

**Analysis Date:** 2026-03-02

## Tech Debt

### Missing Test Coverage
- **Issue:** No test files exist in the codebase
- **Files:** None found (searched for `*.test.*`, `*.spec.*`)
- **Impact:** No automated verification of functionality. Any refactoring or new features have high regression risk.
- **Fix approach:** Add Jest or Vitest with unit tests for utility functions, API routes, and key components.

### Firestore Permissions Issue (Fallback Mode)
- **Issue:** Dashboard runs in fallback mode due to Firestore session permissions
- **Files:** `frontend/src/app/dashboard/page.tsx` (line 216)
- **Impact:** Rename and delete functions are temporarily disabled. Users see a degraded experience with message: "Dashboard is running in fallback mode due Firestore session permissions. Resume works; rename/delete are temporarily disabled."
- **Fix approach:** Review Firestore security rules and data structure to ensure proper session access permissions.

### Minimal Backend Server
- **Issue:** The Express backend is minimal - only contains health check endpoint
- **Files:** `backend/src/index.ts`
- **Impact:** No actual backend business logic. All processing appears to be client-side or via Next.js API routes, which may have cold start latency and limited scalability.
- **Fix approach:** Determine if backend should host heavy processing (file parsing, AI inference) or if current Next.js API route architecture is intentional.

## Known Bugs

### Study Session Recovery Message
- **Issue:** Users may see "Session context missing" message when accessing sessions
- **Files:** `frontend/src/app/dashboard/page.tsx` (lines 379-386)
- **Symptoms:** When a strategyId is in URL but session not found in Firestore
- **Trigger:** Direct navigation to `/dashboard?id=invalid-id`
- **Workaround:** Users can select from recent strategies or create new session

## Security Considerations

### API Key Handling in Custom Model Feature
- **Risk:** Custom model API keys are sent from frontend to API routes
- **Files:** `frontend/src/components/UploadForm.tsx` (lines 283-288, 356-357)
- **Current mitigation:** Keys are sent in request body to Next.js API routes
- **Recommendations:**
  - Consider using Firebase Cloud Functions for API calls to keep keys server-side
  - If client-side AI calls are required, ensure keys are stored securely and not exposed in network tabs
  - Add rate limiting to prevent API key abuse

### Client-Side Only Processing
- **Risk:** Heavy processing (PDF parsing, document analysis) happens client-side
- **Files:** `frontend/src/lib/parsing/` - various parsers
- **Impact:** Users with low-end devices may experience browser freezing. Large files may exceed browser memory limits.
- **Recommendations:** Consider moving heavy parsing to server-side API routes or cloud functions.

## Performance Bottlenecks

### Large File Upload Processing
- **Problem:** All file uploads happen sequentially in browser
- **Files:** `frontend/src/components/UploadForm.tsx` (lines 183-205)
- **Cause:** Sequential `await` in loop for file uploads
- **Improvement path:** Use parallel uploads with `Promise.all` for independent files

### Polling-Based Job Status Checking
- **Problem:** Frontend polls for job completion with 1.2 second intervals
- **Files:** `frontend/src/components/UploadForm.tsx` (lines 369-406)
- **Cause:** Up to 180 polling attempts (~3.6 minutes max wait)
- **Impact:** Unnecessary network requests, battery drain on mobile
- **Improvement path:** Consider WebSocket or server-sent events for real-time updates

### Large Component Bundle
- **Problem:** Single large component (UploadForm.tsx - 811 lines)
- **Files:** `frontend/src/components/UploadForm.tsx`
- **Impact:** Code splitting not optimized. Entire form loads even if user only wants to view existing sessions.
- **Improvement path:** Split into smaller composed components with lazy loading

## Fragile Areas

### AI Response Parsing
- **Files:** `frontend/src/lib/ai/ai-client.ts` (lines 52-80)
- **Why fragile:** Relies on regex to extract JSON from AI response. Any malformed AI output could crash parsing.
- **Safe modification:** Add robust error handling with try-catch and fallback defaults
- **Test coverage:** No tests exist - high risk area

### Type Normalization Functions
- **Files:** `frontend/src/lib/ai/types.ts`
- **Why fragile:** Multiple places use `normalizeStrategyResult()` to handle varying AI response structures. If schema changes, normalization may fail silently.
- **Safe modification:** Add validation with explicit error throwing for unexpected schemas

## Scaling Limits

### Firestore Reads on Dashboard Load
- **Current capacity:** Each dashboard visit queries up to 30 recent strategies
- **Limit:** Could become expensive at scale (Firestore charges per read)
- **Scaling path:** Implement pagination, caching, or CDN for read-heavy dashboards

### File Storage
- **Current capacity:** Files stored in Firebase Storage under user paths
- **Limit:** No apparent cleanup mechanism for deleted sessions
- **Scaling path:** Add lifecycle rules or manual cleanup for orphaned files

## Dependencies at Risk

### Heavy Client-Side Dependencies
- **Package:** `pdf-parse`, `mammoth`, `jspdf`, `jszip`
- **Risk:** Large bundle sizes, parsing happens in main thread
- **Impact:** Slow initial load, potential browser crashes with large files
- **Migration plan:** Consider server-side parsing or Web Workers

### Firebase SDK Versions
- **Package:** `firebase` ^12.0.0, `firebase-admin` ^13.7.0
- **Risk:** Major version differences between client and admin SDKs
- **Impact:** Potential API incompatibilities
- **Migration plan:** Align SDK versions and test thoroughly on updates

## Missing Critical Features

### Error Boundary
- **Problem:** No React error boundary to catch component crashes
- **Impact:** Entire app crashes on any unhandled error
- **Blocks:** User cannot recover from errors without full page refresh

### Offline Support
- **Problem:** No service worker or offline caching
- **Impact:** App unusable without network
- **Blocks:** Study on-the-go in poor connectivity

### Session Persistence
- **Problem:** No localStorage/IndexedDB fallback for offline access
- **Impact:** Users lose progress if network drops during study session

## Test Coverage Gaps

### No Unit Tests
- **What's not tested:** All utility functions, parsers, AI client, Firestore wrappers
- **Files:** `frontend/src/lib/**/*.ts`
- **Risk:** Silent failures in data transformation, parsing errors not caught
- **Priority:** High

### No Integration Tests
- **What's not tested:** API routes, file upload flows, auth flows
- **Files:** `frontend/src/app/api/**/*.ts`
- **Risk:** API contract changes break frontend silently
- **Priority:** High

### No E2E Tests
- **What's not tested:** Complete user flows (upload → generate → study)
- **Risk:** Critical path failures only discovered in production
- **Priority:** Medium

---

*Concerns audit: 2026-03-02*
