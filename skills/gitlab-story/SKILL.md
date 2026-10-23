---
name: gitlab-story
description: Fetch a GitLab issue (story) with its description, acceptance criteria, labels, linked issues and comment decisions, and save it as a structured story.md. Use when a developer starts work on a GitLab story or asks for story context.
---

# GitLab story intake

## Fetching
Use the tools of the MCP server named `gitlab`. Tool names differ between GitLab's built-in MCP server and community servers, so pick the ones that do these jobs:

1. **Get the issue** by project and issue IID: title, description, labels, milestone, assignee, state, web URL.
2. **Get the issue's comments/notes.** Skip system notes (label changes, assignments) and bot comments.
3. **Get linked issues** (title, IID and link type only, do not fetch their full content).
4. If the story references a parent epic, record its title and link only.

If the user gives only `#482`, resolve the project from the current repository's git remote. If that fails, ask for the project path once.

If no `gitlab` tools are available, tell the user the GitLab MCP server is not configured (see the toolkit README) and offer to work from a story they paste in instead, saving it in the same format.

## Where acceptance criteria usually live
Look in this order: a heading such as "Acceptance criteria" / "AC" / "Definition of done" in the description; a task list (`- [ ]`) in the description; Given/When/Then blocks; comments where the product owner confirms or changes criteria. Later confirmed changes in comments override the description; note that you applied them.

## Output: `docs/ai/stories/<iid>-<short-slug>/story.md`

```markdown
# <iid>: <title>

| Field | Value |
|---|---|
| GitLab | <web URL> |
| State | <open/closed> |
| Labels | <comma-separated> |
| Milestone | <name or —> |
| Fetched | <YYYY-MM-DD> |

## Summary
<2–4 sentences in plain language: who needs what, and why.>

## Acceptance criteria
- **AC-1** <criterion, faithfully copied; keep Given/When/Then if used>
- **AC-2** …

## Inferred acceptance criteria (needs confirmation)
<only if the story had none or they were incomplete; otherwise omit this section>

## Business context and rules
<constraints, rules, data or compliance requirements stated in the story or comments>

## Decisions from comments
- <YYYY-MM-DD, author role> <decision or clarification>

## Linked issues
- <type>: #<iid> <title>

## Out of scope
<anything explicitly excluded>

## Open questions
- <ambiguities a developer must resolve before or during implementation>
```

Keep the whole file under about 120 lines. It is read by several agents, so every extra line costs tokens repeatedly.
