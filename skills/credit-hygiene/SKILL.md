---
name: credit-hygiene
description: Habits that keep GitHub Copilot AI-credit usage low during agent work — file handoffs, fresh sessions, narrow tools, delegating searches. Use when planning a multi-step task, when a session is getting long, or when the user asks how to reduce credit usage.
---

# Credit hygiene

Credits are charged on tokens: everything sent to the model on every turn (instructions, tool definitions, conversation history, file contents, tool output) plus everything it writes. Most waste comes from re-sending context that was already processed.

## Rules for agents
1. **Hand off through files.** End each phase by writing its result to a file (`story.md`, `spec.md`, tests). The next phase starts a fresh session and reads the file, instead of carrying the whole conversation forward.
2. **Read the map first.** Read `docs/ai/app-overview.md` before searching the codebase. Open specific files only after that.
3. **Delegate wide searches.** Use the Explore subagent for "find all X" questions and keep only its summary in the main session.
4. **Read slices, not whole files.** For large files, search for the symbol and read the surrounding lines.
5. **Don't re-fetch.** Never call GitLab again for a story that already has a `story.md`.
6. **Batch mechanical loops.** For repetitive checks (many similar commands or queries), write or run one script that returns a summary instead of many tool calls.
7. **Short replies.** Summarise what changed and where; do not echo file contents back to the user.

## Habits for developers (put these in the team README)
- Start a **new chat for each phase or task**. Use `/fork` for side questions instead of derailing the main session.
- Use `/compact` when a long session must continue.
- Use **Auto** model selection by default; the toolkit's agents pin models only where it matters.
- Use **Configure Tools** to switch off MCP servers you don't need in a session.
- Exclude build output and generated files from search (`search.exclude`, `.gitignore`).
- Check cost per turn by hovering over a response, and run `/chronicle:cost-tips` occasionally.
