# Technology Stack

**Analysis Date:** 2026-03-02

## Languages

**Primary:**
- TypeScript 5.x - All frontend and backend code
- JavaScript - Legacy backend support

**Secondary:**
- CSS - TailwindCSS for styling

## Runtime

**Environment:**
- Node.js 18+ (required)
- Next.js 16.1.x (API routes and frontend framework)

**Package Manager:**
- npm (npm install)
- Lockfiles: `package-lock.json` present in both frontend and backend

## Frameworks

**Core:**
- Next.js 16.1.6 - Full-stack framework with API routes and React frontend
- React 19.0.0 - UI library
- Express 5.2.1 - Backend API server (separate backend folder)

**Testing:**
- Not configured yet (no test runner detected)

**Build/Dev:**
- TypeScript 5.9.3 - Type checking and compilation
- ESLint 9 - Code linting
- TailwindCSS 4 - Styling framework
- shadcn/ui - Component library (built on Radix UI)
- nodemon 3.1.14 - Development auto-reload for backend

## Key Dependencies

**Frontend Critical:**
- `firebase` 12.0.0 - Client-side Firebase SDK
- `firebase-admin` 13.7.0 - Server-side Firebase Admin SDK
- `jspdf` 4.2.0 - Client-side PDF generation for strategy reports
- `pdf-parse` 1.1.1 - PDF text extraction
- `mammoth` 1.9.1 - DOCX file parsing
- `lucide-react` 0.575.0 - Icon library
- `framer-motion` 12.34.3 - Animation library
- `react-markdown` 10.1.0 - Markdown rendering
- `youtube-transcript` 1.2.1 - YouTube transcript extraction

**Backend Critical:**
- `express` 5.2.1 - HTTP server framework
- `cors` 2.8.6 - Cross-origin resource sharing
- `dotenv` 17.3.1 - Environment variable loading
- `ts-node` 10.9.2 - TypeScript execution
- `typescript` 5.9.3 - TypeScript compiler

**UI Components:**
- `@radix-ui/react-slot` 1.2.4 - Component composition
- `@radix-ui/react-avatar` 1.1.11 - Avatar component
- `class-variance-authority` 0.7.1 - Component variant styling
- `tailwind-merge` 3.5.0 - Tailwind class merging utility

## Configuration

**Environment:**
- `.env.local` files for local development
- Firebase configuration via environment variables
- AI provider configuration (AI_PROVIDER, AI_API_KEY)

**Build:**
- `tsconfig.json` in backend - TypeScript configuration
- `next.config.ts` - Next.js configuration
- `tailwind.config` (v4 uses CSS-based config)
- `eslint.config.mjs` - ESLint configuration

## Platform Requirements

**Development:**
- Node.js 18+
- Firebase project with Firestore enabled
- API keys for LLM providers (Gemini, OpenAI, or custom)

**Production:**
- Firebase Hosting (via firebase.json)
- Firestore database
- Firebase Storage (for uploaded files)
- Firebase Authentication

---

*Stack analysis: 2026-03-02*
