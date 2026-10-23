---
name: spec-template
description: Required structure for technical specs (spec.md) written from a GitLab story, with acceptance-criteria traceability for coding, test and review agents. Use when writing or reviewing a technical spec.
---

# Technical spec template

Write `spec.md` in the same folder as `story.md`. Use these sections, in this order. Omit a section only if it truly does not apply, and say "None" rather than deleting it when a reader would otherwise wonder.

```markdown
# Spec: <iid> <title>

Story: [story.md](./story.md) · Status: Draft · Author: <name> + AI · Date: <YYYY-MM-DD>

## Blocking questions
- <questions that would change the design; empty list if none>

## Approach
<one short paragraph: the chosen approach and why. Mention a rejected alternative in one line if it was close.>

## Acceptance criteria traceability
| AC | Implemented by (section / file) | Verified by (test) |
|---|---|---|
| AC-1 | Backend §2, `OrderService.cs` | `OrderServiceTests.Export_FiltersByDate` |

## Changes
### Backend
1. `path/to/File.cs` (new | change): <what and why>
### Database
<migrations, schema changes, data backfill; "None" if none>
### Frontend
1. `path/to/Component.tsx` (new | change): <what and why>
### Configuration
<settings, feature flags, secrets needed (names only, never values)>

## API contract
<new or changed endpoints: method, route, request and response shape, status codes, auth>

## Flow
1. <numbered steps for the main path>
2. …
Error paths:
- <what happens when …>

## Tests
### Unit
- <test name>: <what it proves> (AC-n)
### Integration
- …
### Manual or E2E checks
- …

## Risks and rollout
<data migration risk, performance, backwards compatibility, feature flag, rollback>

## Out of scope
- …
```

## Rules
- Every `AC-n` appears in the traceability table. If one cannot be implemented as written, it goes in Blocking questions.
- File paths must exist in the repo (for changes) or follow the conventions in `docs/ai/app-overview.md` (for new files).
- Name tests the way the repo already names them.
- Prefer precise nouns over prose: route names, class names, column names.
- Target length: 100–250 lines.
