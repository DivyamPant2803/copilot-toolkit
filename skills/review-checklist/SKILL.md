---
name: review-checklist
description: What to check when reviewing a .NET / React change against its story and spec, how to rank findings, and the exact review report format. Use when reviewing a branch, diff or merge request.
---

# Review checklist

## 1. Requirements (do this first; it's what generic review tools miss)
- For every `AC-n`: is it implemented? Is there a test that would fail without it? Mark each **Met / Partly met / Not met / Can't tell**.
- **Out-of-scope changes:** files or behaviour the spec doesn't mention. AI-generated code often refactors or reformats unrelated code; list each one so the reviewer can ask for it to be split out.
- Business rules from the story or `app-overview.md`: are they enforced on the **server**, not only in the UI?

## 2. Correctness and data
- EF Core: N+1 queries (lazy loading in loops, missing `Include`), unbounded queries (no paging on lists), `ToList()` before filtering, missing `AsNoTracking` on read paths where the repo uses it.
- Migrations: present when the schema changes; safe for existing data (non-null columns need defaults or backfill); destructive operations called out.
- Transactions and concurrency for multi-step writes.
- Null handling, error paths, and status codes match the spec's API contract.
- Async all the way: no `.Result` / `.Wait()`, `CancellationToken` passed through where the repo does.

## 3. Security and compliance
- Authorisation on every new endpoint, consistent with existing policies. Check ownership/tenant checks, not just `[Authorize]`.
- Input validation on the server; no raw SQL string concatenation.
- No secrets, connection strings, tokens or real customer data in code, tests, config or logs. No PII written to logs.
- Frontend: no `dangerouslySetInnerHTML` with user data; no sensitive data in local storage.
- New packages: needed, maintained, and licence-compatible? Flag every new dependency.

## 4. Tests
- Tests assert behaviour, not implementation details.
- Tests weren't weakened to pass (deleted assertions, broadened expectations, skipped tests).
- Edge cases from the spec are covered.

## 5. Conventions and maintainability
- Follows the "Conventions" section of `app-overview.md`: naming, layering, DTO mapping, error handling.
- Duplication of existing helpers or services (search before claiming).
- Dead code, commented-out code, leftover `TODO`s without a story reference.

## Severity
- **Blocker:** wrong behaviour against an AC, data loss risk, security or compliance issue, build or tests failing.
- **Major:** likely bug, missing test for an AC, migration risk, significant convention break.
- **Minor:** maintainability, naming, small inefficiency.
- **Question:** you're not sure; the author should confirm.
Skip pure style nits that a formatter or linter would catch.

## Report format
```markdown
# Review: <iid> <title>
Target: <branch or !MR> · Files: <n changed> · Build: <pass/fail/not run> · Tests: <x passed, y failed / not run>

## Verdict
<Ready to merge | Merge after fixes | Needs rework> — <one sentence why>

## Acceptance criteria
| AC | Status | Evidence |
|---|---|---|
| AC-1 | Met | `OrderExportService.cs:42`, test `Export_FiltersByDateRange` |

## Findings
### Blocker
1. **<title>**: `path:line`. <what is wrong, why it matters, suggested fix in 1–3 lines>
### Major
### Minor
### Questions

## Out-of-scope changes
- `path`: <what changed>. Suggest: <keep / split into separate MR / revert>

## Overview impact
<"No overview impact" — or, per section of docs/ai/app-overview.md:>
- **<Section name>**: <what is now wrong or missing>
  Proposed text: `<exact replacement or new line, with file citation>`

## Where to look first
<the 3–5 files a human reviewer should read closely, in order, and why>
```

## Overview impact rules
The overview needs updating when the diff:
- adds or removes an API area, project, entity/table, background job or external integration;
- adds, changes or removes a business rule the overview lists (or should list);
- introduces a new pattern other code should copy, or abandons an existing convention;
- changes build, run or test commands, frameworks or key package versions;
- moves or renames something the overview cites (a broken file reference counts).

It does **not** need updating for ordinary bug fixes, new fields on existing entities, UI tweaks, or new code that follows existing patterns. Say "No overview impact" in that case; most stories land here.

Proposed text must be written in the overview's existing style, concise, with a file citation, ready to paste. If the change is large (a new module), propose running Repo Assessor in "update from diff" mode instead of listing many lines.
Keep the report under about 150 lines. Omit empty severity headings.
