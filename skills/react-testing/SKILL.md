---
name: react-testing
description: Conventions for writing React component and hook tests with Jest or Vitest and React Testing Library in TDD style, including API mocking and running a filtered set of tests. Use when writing or fixing frontend tests.
---

# React testing

## First, match the repo
Open one existing test next to a similar component and copy: runner (Jest or Vitest), file naming (`*.test.tsx` vs `*.spec.tsx`, co-located vs `__tests__`), render helper (many repos wrap providers in a custom `renderWithProviders`), API mocking approach (MSW, mocked API client module, or `fetch` mock) and test data builders. **The repo's existing style wins.**

## Principles
- Test what the user sees and does, not implementation details. Query by role, label or text: `getByRole('button', { name: /export/i })`. Use `data-testid` only if the repo already relies on it.
- Use `userEvent` over `fireEvent` for interactions; `await` it.
- Async UI: `findBy…` or `waitFor`; never fixed timeouts.
- Cover the states the spec names: loading, empty, error, success, and permission-restricted views.
- Mock at the network boundary (MSW or the API client), not child components, unless the repo does otherwise.
- Hooks with logic: test through a small component or `renderHook`.

## TDD stubs
If the component doesn't exist yet, create a minimal placeholder so the import resolves:
```tsx
// TODO(<iid>): implement
export function AuditLogExport() {
  return null;
}
```
The tests should then fail on missing elements, which is the right reason.

## Running only the new tests
```bash
npx jest src/features/audit/AuditLogExport.test.tsx
npx vitest run src/features/audit/AuditLogExport.test.tsx
```
Use the repo's npm script if it wraps the runner (check `package.json`).
