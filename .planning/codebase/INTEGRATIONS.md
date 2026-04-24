# External Integrations

**Analysis Date:** 2026-03-02

## APIs & External Services

**AI/ML Providers:**
- **Google Gemini** - Primary AI model for strategy generation, chat Q&A, and exam mode
  - SDK: Custom implementation via `generateWithGeminiModel` in `frontend/src/lib/ai/providers/gemini.ts`
  - Config: `AI_PROVIDER=gemini` in environment
  - Also supports custom OpenAI-compatible endpoints

**YouTube Integration:**
- YouTube transcript extraction via `youtube-transcript` package
  - Implementation: `frontend/src/lib/parsing/youtube.ts`
  - Used for video-based study content

## Data Storage

**Firestore (NoSQL):**
- Database: Firebase Firestore (default, asia-south1)
- Connection: Firebase Admin SDK with service account credentials
- Client: `firebase` 12.0.0 (client-side), `firebase-admin` 13.7.0 (server-side)
- Collections:
  - `strategies/{strategyId}` - AI-generated study strategies
  - `users/{uid}/studySessions/{sessionId}` - User progress and session data
  - `users/{uid}/preferences` - User settings
  - `sources/{sourceId}` - Uploaded study materials

**Firebase Storage:**
- File storage for uploaded PDFs, DOCXs, PPTXs
- Rules: `storage.rules`
- Automatic cleanup of uploaded files

**Local File Parsing:**
- PDF: `pdf-parse` for text extraction
- DOCX: `mammoth` for document parsing
- PPTX: Custom parser in `frontend/src/lib/parsing/parse-pptx.ts`
- All parsing happens client-side (browser)

## Authentication & Identity

**Firebase Authentication:**
- Implementation: `frontend/src/lib/firebase.ts`
- Auth provider: Firebase Auth (email/password, OAuth)
- Session management via Firebase Auth sessions
- Server-side auth: `frontend/src/lib/server/auth.ts`
- Protected routes: `frontend/src/components/RequireAuth.tsx`

## Monitoring & Observability

**Error Tracking:**
- Not explicitly configured (no Sentry, Crashlytics, or similar)

**Logs:**
- Console logging via Next.js and Express
- Firestore audit logs via Firebase Console

## CI/CD & Deployment

**Hosting:**
- Firebase Hosting (configured in `firebase.json`)
- Source: `frontend` directory
- Ignores: node_modules, firebase.json

**CI Pipeline:**
- Not explicitly configured (no GitHub Actions, CircleCI, or similar)

## Environment Configuration

**Required env vars (Frontend):**
```
NEXT_PUBLIC_FIREBASE_API_KEY
NEXT_PUBLIC_FIREBASE_AUTH_DOMAIN
NEXT_PUBLIC_FIREBASE_PROJECT_ID
NEXT_PUBLIC_FIREBASE_STORAGE_BUCKET
NEXT_PUBLIC_FIREBASE_MESSAGING_SENDER_ID
NEXT_PUBLIC_FIREBASE_APP_ID

# Server-side Firebase Admin
FIREBASE_SERVICE_ACCOUNT_KEY (JSON service account)
# OR
FIREBASE_CLIENT_EMAIL
FIREBASE_PRIVATE_KEY
FIREBASE_PROJECT_ID
```

**Required env vars (Backend):**
```
FIREBASE_PRIVATE_KEY
FIREBASE_PROJECT_ID
AI_PROVIDER=gemini  # or openai, custom
AI_API_KEY
```

## Webhooks & Callbacks

**Incoming:**
- None explicitly configured

**Outgoing:**
- AI API calls to Gemini (streaming and non-streaming)
- Custom AI endpoints (when AI_PROVIDER=custom)

---

*Integration audit: 2026-03-02*
