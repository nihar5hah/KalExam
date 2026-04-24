# Testing Patterns

**Analysis Date:** 2026-03-02

## Test Framework

**Status: NOT CONFIGURED**

This codebase has **no test framework** implemented. This is a significant gap in code quality.

### Frontend

- **Test Runner:** None
- **Installed:** No testing packages (no jest, vitest, react-testing-library)
- **Package:** `frontend/package.json` - no test script defined

### Backend

- **Test Runner:** None
- **Installed:** No testing packages
- **Package:** `backend/package.json` has placeholder:
  ```json
  "test": "echo \"Error: no test specified\" && exit 1"
  ```

## Test File Organization

**Location:** No test files exist in the project

- No `__tests__` directories
- No `.test.ts`, `.spec.ts`, `.test.tsx`, `.spec.tsx` files
- All files in `src/` are production code only

## Test Structure

**No patterns to document** - no tests exist in the codebase.

## Mocking

**No mocking infrastructure:**
- No jest mocks
- No sinon
- No msw (Mock Service Worker)
- No nock

## Fixtures and Factories

**No test fixtures or factories** - no testing infrastructure.

## Coverage

**No coverage enforcement:**
- No coverage configuration
- No coverage reporting

## Test Types

**None implemented:**
- Unit tests: Not present
- Integration tests: Not present
- E2E tests: Not present (no Playwright, Cypress)

## Recommendations

### Required Setup

1. **Frontend Testing:**
   ```bash
   npm install --save-dev vitest @vitejs/plugin-react @testing-library/react @testing-library/dom @testing-library/jest-dom jsdom
   ```

2. **Backend Testing:**
   ```bash
   npm install --save-dev vitest @types/jest ts-jest
   ```

3. **Create test directories:**
   - `frontend/src/__tests__/` or `frontend/src/*.test.tsx`
   - `backend/src/__tests__/` or `backend/src/*.test.ts`

4. **Add test scripts to package.json:**
   ```json
   "test": "vitest",
   "test:coverage": "vitest --coverage"
   ```

### Suggested Testing Patterns

**Unit Tests (components):**
```typescript
import { render, screen } from '@testing-library/react';
import { Button } from '@/components/ui/button';

describe('Button', () => {
  it('renders correctly', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toBeInTheDocument();
  });
});
```

**Unit Tests (utilities):**
```typescript
import { describe, it, expect } from 'vitest';
import { cn } from '@/lib/utils';

describe('cn', () => {
  it('merges class names correctly', () => {
    expect(cn('foo', 'bar')).toBe('foo bar');
  });
});
```

**API Route Tests:**
```typescript
import { POST } from '@/app/api/study/ask/route';
import { NextRequest } from 'next/server';

describe('/api/study/ask', () => {
  it('returns 400 for missing topic', async () => {
    const req = new NextRequest('http://localhost/api/study/ask', {
      method: 'POST',
      body: JSON.stringify({ question: 'test?' }),
    });
    const res = await POST(req);
    expect(res.status).toBe(400);
  });
});
```

### Testing Priorities

1. **High Priority:** Utility functions in `frontend/src/lib/` (e.g., `utils.ts`, `types.ts`)
2. **High Priority:** API routes in `frontend/src/app/api/`
3. **Medium Priority:** React components in `frontend/src/components/`
4. **Medium Priority:** Backend Express routes in `backend/src/`
5. **Low Priority:** Integration tests with Firebase (requires mocking)

---

*Testing analysis: 2026-03-02*
