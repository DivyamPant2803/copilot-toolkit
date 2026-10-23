# team-dev-toolkit

A GitHub Copilot agent plugin for VS Code that takes a GitLab story from intake through spec, tests, implementation and review. It is built to keep AI-credit usage low: each phase writes its result to a file and the next phase starts fresh from that file.

**Status:** Phase 1 complete, Phase 2 in progress. Version 0.2.0.

## Components

| Component | Type | What it does | Output |
|---|---|---|---|
| Story Intake | Agent | Fetches a GitLab story once | `docs/ai/stories/<iid>-<slug>/story.md` |
| Repo Assessor | Agent | Maps the application once per repo | `docs/ai/app-overview.md` |
| Spec Writer | Agent | Writes the technical spec from story + overview | `…/spec.md` |
| Test Writer | Agent | Writes failing tests for every acceptance criterion (TDD) | test files + `…/tests.md` |
| Code Reviewer | Agent | Reviews a branch or MR against story, spec and conventions | review report in chat |
| gitlab-story | Skill | How to fetch a story and the `story.md` format | |
| repo-assessment | Skill | Assessment method and overview format | |
| spec-template | Skill | Spec structure with acceptance-criteria traceability | |
| dotnet-testing | Skill | xUnit/NUnit/MSTest, EF Core and API test conventions | |
| react-testing | Skill | Jest/Vitest + React Testing Library conventions | |
| review-checklist | Skill | What to check, severity levels, report format | |
| credit-hygiene | Skill | Habits that keep credit usage low | |

## Workflow

```
Repo Assessor (once per repo)  ──►  docs/ai/app-overview.md
                                            │
GitLab story ──► Story Intake ──► story.md ─┼──► Spec Writer ──► spec.md
                                                                   │
                     Test Writer ◄─────────────────────────────────┘
                         │  failing tests + tests.md
                         ▼
                 Implementation (built-in Agent for now; .NET/React agents coming)
                         │  tests pass
                         ▼
                 Code Reviewer ──► review report ──► human review ──► merge request
```

Start a **new chat** for each step. The files carry the context forward, so there is no need to keep a long conversation going.

## Setup

### 1. Install the plugin
Prerequisite: `chat.plugins.enabled` is on in VS Code.

- Command Palette → **Chat: Install Plugin From Source** → paste this repository's Git clone URL.
- Or clone it and register the folder in your user `settings.json`:
  ```json
  "chat.pluginLocations": { "/path/to/team-dev-toolkit": true }
  ```

Check: the agent picker in Chat should list **Story Intake**, **Repo Assessor**, **Spec Writer**, **Test Writer** and **Code Reviewer**.

### 2. Add the per-repo files to each application repo
Copy from `templates/consumer-repo/`:

- `.github/copilot-instructions.md`: fill in the stack versions. Keep it short; it is sent with every request.
- `.vscode/mcp.json`: set your GitLab host. The server **must** be named `gitlab`.

### 3. Connect GitLab
Open `.vscode/mcp.json` and start the `gitlab` server. Check with **MCP: List Servers**.

GitLab's built-in MCP server needs GitLab 18.7 or later, Premium/Ultimate, and must be enabled by your GitLab admin. If your instance doesn't meet that, use Option B in the template (a locally run server with a read-only token).

### 4. First run on a repository
1. Select **Repo Assessor** → "Assess this repository."
2. Review `docs/ai/app-overview.md` with someone who knows the app. Fix mistakes and confirm the `(unverified)` items. Every later agent trusts this file.
3. Commit `docs/ai/`.

## Daily use

1. New chat → **Story Intake** → `#482`
2. Read `story.md`, answer the open questions.
3. New chat → **Spec Writer** → `docs/ai/stories/482-<slug>`
4. Review `spec.md`; it's the contract for implementation and review.
5. New chat → **Test Writer** → the same story folder. Check the tests fail for the right reason and read `tests.md`.
6. Select **Implement to make tests pass** (or start a new chat with the built-in Agent and point it at `spec.md` and `tests.md`).
7. New chat → **Code Reviewer** → `docs/ai/stories/482-<slug> against develop`, or an MR such as `!1234`.
8. Fix blockers and majors, then open the merge request. Use the report's "Where to look first" section to focus the human review.

### A note on the Code Reviewer
It has no file-editing tools, but it does have the terminal (`execute`) so it can run `git diff`, the build and the tests. Its instructions forbid changes; approve its terminal commands as usual. It never posts to GitLab; paste the parts of the report you want into the MR yourself.

## Customising the models

Agent files leave `model:` commented out, so they use whatever is selected (Auto recommended). To pin a model, uncomment it and set one your organisation allows. Suggested split: a low-cost model for Story Intake; a mid-cost model for Test Writer; a capable model for Repo Assessor, Spec Writer and Code Reviewer.

## Roadmap

| Phase | Components |
|---|---|
| 1 ✔ | Story intake, repo assessment, spec writer, credit hygiene |
| 2 | ✔ Test writer (TDD), ✔ code review agent; next: .NET and React coding agents, code conflict fixer |
| 3 | Automation (E2E) testing, diagram generator, vulnerability pre-scan and remediation, usage reporting |

## Contributing
- Bump `version` in `plugin.json` for every change you want teams to pick up.
- Skill folder names must match the `name` in their `SKILL.md` and be plain kebab-case.
- Test changes by registering your local clone with `chat.pluginLocations` before pushing.
