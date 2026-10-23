---
name: repo-assessment
description: Method and output format for building docs/ai/app-overview.md, a persistent map of a .NET / React application's structure, data model, flows and business rules. Use when assessing or refreshing a repository overview.
---

# Repository assessment

## Method (cheapest useful order)
1. **Inventory.** List solution and project files (`*.sln`, `*.csproj`), `package.json` files, `Dockerfile`s, CI files (`.gitlab-ci.yml`), and top-level folders. This gives the shape of the system without opening code.
2. **Entry points.** `Program.cs` / `Startup.cs` (DI registrations, middleware, auth), React entry (`main.tsx`/`index.tsx`), router configuration.
3. **API surface.** Controllers or minimal-API endpoint groups: route prefixes and what each area does. Do not list every endpoint; group them.
4. **Data.** DbContext(s), entity list, key relationships, migrations folder, any raw SQL or stored procedures. Note the database type.
5. **Cross-cutting.** Authentication and authorisation model, logging, configuration sources, background jobs, external integrations (queues, blob storage, third-party APIs).
6. **Frontend.** State management, API client layer, component library, main feature areas.
7. **Tests.** Test projects, frameworks (xUnit/NUnit/MSTest, Jest/Vitest, RTL, Playwright), what is covered, how to run them.
8. **Business rules.** Explicit rules found in validators, domain services, status enums/state machines, permission checks. Cite file paths.
9. **Conventions.** Naming, folder-per-feature vs layer, DTO/mapping approach, error handling pattern, async patterns. These are what coding agents need most.

For steps that need many files (3, 4, 8), delegate the sweep to the Explore subagent and keep only the summary.

## Output: `docs/ai/app-overview.md`

```markdown
# <Application name>: overview for AI agents

Last assessed: <YYYY-MM-DD> at commit <short sha>

## Purpose
<3–5 sentences: what the app does and for whom.>

## Architecture
<projects/apps and how they talk to each other; hosting; a numbered request flow for the main path>

## Repository map
| Path | What lives there |
|---|---|

## Backend (.NET)
### API areas
### Data model
### Cross-cutting concerns

## Frontend (React)

## External integrations

## Business rules
- <rule> (`path/to/File.cs`)

## Conventions coding agents must follow
- <convention> (example: `path/to/Example.cs`)

## Testing
<frameworks, locations, how to run: exact commands>

## Build and run
<exact commands>

## Known gaps and risks
<tech debt, missing tests, (unverified) items>
```

## Quality bar
- Every claim about behaviour points to a file.
- Commands are exact and copy-pasteable.
- No code blocks longer than 5 lines; link to the file instead.
- No secrets, connection strings, hostnames of production systems, or customer data.
