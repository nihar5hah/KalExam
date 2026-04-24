# Coding Conventions

**Analysis Date:** 2026-03-02

## Naming Patterns

**Files:**
- PascalCase for components: `AuthProvider.tsx`, `StudyChatPanel.tsx`
- kebab-case for utilities and lib files: `firebase.ts`, `rag.ts`, `modelRouter.ts`
- Route files: kebab-case `route.ts`

**Functions:**
- camelCase: `getFirebaseApp()`, `answerTopicQuestion()`, `resolveModelConfig()`
- Verb-noun pattern: `getAuthenticatedUid()`, `extractBearerToken()`

**Variables:**
- camelCase: `authenticatedUid`, `firebaseConfig`, `modelConfig`
- Type-safe with explicit typing for function parameters and return values

**Types:**
- PascalCase: `ModelType`, `StrategyResult`, `StudyTopic`, `UploadedFile`
- Union types for enums: `"gemini" | "custom"`, `"high" | "medium" | "low"`

## Code Style

**Formatting:**
- Tool: ESLint with `eslint-config-next` (Next.js recommended rules)
- No custom Prettier config (uses defaults)
- TypeScript strict mode enabled in both `frontend/tsconfig.json` and `backend/tsconfig.json`

**Linting:**
- Frontend: `eslint-config-next/core-web-vitals` + `eslint-config-next/typescript`
- Config file: `frontend/eslint.config.mjs`
- Strict TypeScript enabled (`"strict": true`)

## Import Organization

**Order (in frontend):**
1. React/Next.js imports: `"react"`, `"next/server"`
2. External libraries: `"firebase/app"`, `"firebase/auth"`
3. Path aliases (`@/`): `@/lib/firebase`, `@/components/AuthProvider`
4. Relative imports: `../components`, `./utils`

**Path Aliases:**
- `@/*` maps to `./src/*` (defined in `frontend/tsconfig.json`)
- Used throughout the codebase: `@/lib/firebase`, `@/components/ui/button`

## Error Handling

**Patterns:**
- Custom error classes extending `Error`:
  ```typescript
  export class RequestAuthError extends Error {
    status: number;
    constructor(message: string, status = 401) { ... }
  }
  ```
- Error throwing in auth: `throw new RequestAuthError("Invalid authentication token", 401)`
- API routes use try-catch with error type checking:
  ```typescript
  if (error instanceof RequestAuthError) {
    return NextResponse.json({ error: error.message }, { status: error.status });
  }
  ```
- Validation errors return 400: `NextResponse.json({ error: "Missing topic" }, { status: 400 })`
- Forbidden errors return 403: `NextResponse.json({ error: "Forbidden" }, { status: 403 })`

## Logging

**Framework:** `console.log` in backend, no structured logging in frontend

**Patterns:**
- Backend startup: `console.log('Server is running on http://localhost:${PORT}')`
- No structured logging library (e.g., Winston, Pino)
- No log levels (debug, info, warn, error)

## Comments

**When to Comment:**
- Complex type definitions: JSDoc comments in `frontend/src/lib/ai/types.ts`
- Utility functions for data normalization
- No inline comments in components for basic logic

**TSDoc:**
- Not extensively used in this codebase

## Function Design

**Size:** Medium-sized functions, some large type normalization functions in `types.ts` (650+ lines)

**Parameters:**
- Explicit typing for all parameters
- Destructuring for objects: `function getAuthenticatedUid(request: Request)`
- Optional parameters with defaults

**Return Values:**
- Explicit return type annotations: `export async function POST(request: Request): Promise<NextResponse>`

## Module Design

**Exports:**
- Named exports preferred: `export function getFirebaseApp()`, `export class RequestAuthError`
- Default exports not used in this codebase

**Barrel Files:**
- Not used - imports are direct to files

## Component Patterns

**React Components:**
- Use `"use client"` directive for client-side interactivity
- Context pattern with `createContext`:
  ```typescript
  const AuthContext = createContext<AuthContextValue | null>(null);
  ```
- Custom hooks: `useAuth()` with context validation
- Memoization with `useMemo`
- Variant patterns using `class-variance-authority` (cva):
  ```typescript
  const buttonVariants = cva("...", { variants: { variant: {...}, size: {...} } });
  ```

## Tailwind CSS Patterns

**Utility Usage:**
- Tailwind CSS v4 with `@tailwindcss/postcss`
- Custom `cn()` utility combining `clsx` + `tailwind-merge`:
  ```typescript
  export function cn(...inputs: ClassValue[]) {
    return twMerge(clsx(inputs));
  }
  ```
- Component-scoped classes with shadcn/ui patterns

---

*Convention analysis: 2026-03-02*
