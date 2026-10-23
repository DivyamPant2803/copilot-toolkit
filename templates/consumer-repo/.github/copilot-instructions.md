# Copilot instructions for <Application name>

<!-- Keep this file short: it is sent with EVERY request. Detail belongs in docs/ai/app-overview.md and in skills. -->

## Context
- Before searching the code, read `docs/ai/app-overview.md`. It maps the architecture, data model, business rules and conventions.
- Story context lives in `docs/ai/stories/<iid>-<slug>/story.md` and the design in `spec.md` next to it. Read those instead of fetching the story again.

## Stack
- Backend: .NET <version>, ASP.NET Core, EF Core, SQL Server
- Frontend: React <version>, TypeScript, <state library>, <component library>
- Tests: <xUnit/NUnit>, <Jest/Vitest> + React Testing Library

## Non-negotiables
- Follow the conventions section of `docs/ai/app-overview.md`.
- Never put secrets, connection strings or real customer data in code, tests, logs or docs.
- Every change traces to an acceptance criterion (`AC-n`) in the spec.
- Run the build and the relevant tests before saying a task is done.

## Working style
- Keep replies short: what changed, where, and anything the developer must decide.
- Delegate broad codebase searches to the Explore subagent.
