# Project Pulse implementation plan

## 1. Project summary

Project Pulse is a lightweight, static dashboard that helps contributors quickly
understand which projects are active, who owns them, their status, recent
activity, and priority or risk level. The finished experience must feel like a
polished dashboard rather than a bare HTML page: it needs readable information
hierarchy, project cards, status badges, priority treatment, and a responsive,
accessible layout.

The repository currently provides the brief and custom agent definitions, but
the dashboard implementation files are to be created. The deliverable is a
small dependency-free static app:

- `app/index.html`
- `app/styles.css`
- `app/project-data.json`
- `.vscode/launch.json`

The app should render cards from the JSON data rather than duplicating project
content as hard-coded HTML. The supplied brief requires a JSON top-level
`projects` array. Every project object must include `name`, `owner`, `status`,
`recentActivity`, and `priority`. A short contributor-friendly summary should
also be included in the data and rendered by the page so the dashboard meets
the stated user need.

## 2. Agent responsibilities and file ownership

The Orchestrator coordinates phases and integration, the Planner supplies this
plan, the Designer owns presentation, and the Coder owns behavior, data, and
runnable-app support. No agent should edit a file outside its explicit scope.

| Agent | Responsibility | Owned files / scope | Required handoff |
| --- | --- | --- | --- |
| Orchestrator | Translate the plan into non-conflicting phases, issue explicit scopes, resolve blockers, and verify the integrated dashboard. It coordinates rather than implements. | No implementation files. Coordination and final integration review only. | Phase status, accepted contracts, blockers, and final review outcome. |
| Planner | Research repository requirements, identify dependencies and risks, define order and validation. Does not write app code. | `docs/project-pulse-plan.md` | The ordered plan, ownership boundaries, validation checklist, and open decisions. |
| Designer | Define and implement visual hierarchy, responsive layout, accessibility-oriented presentation, card/badge/priority styling, and deterministic CSS hooks. | `app/styles.css` only | CSS hook contract, design decisions, responsive/accessibility checks, and integration recommendations. |
| Coder | Implement deterministic, data-driven rendering; create valid sample project data; provide the VS Code launch support; make errors explicit. | `app/project-data.json`, `.vscode/launch.json`, then `app/index.html` in the integration phase | Data contract/sample values, launch verification, rendering/error behavior, validation results, and remaining risk. |

### Explicit file assignments

| File | Assigned agent | Scope and acceptance criteria |
| --- | --- | --- |
| `app/index.html` | Coder, **only after** the data contract and Designer hook guidance are available | Static document structure and JavaScript that fetches `project-data.json`, validates/renders the project list, and applies agreed styling hooks. It must render all project cards from data, contain a useful loading/error state, and include no separate styling work. |
| `app/styles.css` | Designer | Complete polished presentation layer. It must define and use `.dashboard` and `.project-card` hooks; visually distinguish statuses and priorities; use readable spacing, contrast, `border-radius`, and `box-shadow`; and support small and large screens. |
| `app/project-data.json` | Coder | Valid JSON with top-level `projects` array. Each object has `name`, `owner`, `status`, `recentActivity`, `priority`, plus a contributor-friendly summary field agreed in the contract. Values should be realistic, diverse, and safe to display. |
| `.vscode/launch.json` | Coder | Strict, comment-free JSON. It must define a configuration named `Run Project Pulse Dashboard`, use `cwd` `${workspaceFolder}/app`, run `python3 -m http.server 5500`, and use the server-ready URL `http://localhost:%s/index.html`. |

## 3. Shared contract before implementation

Before parallel work begins, the Orchestrator records and shares this small
contract with the Designer and Coder:

1. The JSON root is `{ "projects": [ ... ] }`.
2. Every project includes string values for `name`, `owner`, `status`,
   `recentActivity`, and `priority`.
3. Each project also includes a concise `summary` string for the
   contributor-friendly explanation required by the brief.
4. `status` and `priority` use a finite, documented set of displayable values
   selected by the Coder; the Designer is given those exact values so CSS badge
   selectors and labels align with the data.
5. HTML uses semantic elements and the Designer's stable class hooks:
   `.dashboard` for the primary dashboard wrapper and `.project-card` for each
   generated project card. Additional hook names for card fields, badges, and
   priority indicators are provided by the Designer before HTML is authored.
6. Renderer output treats JSON values as text, not HTML, to avoid content
   injection through data. Missing/invalid required values result in an
   explicit, user-visible error rather than a malformed card.

## 4. Ordered implementation steps

1. **Confirm baseline and brief requirements — Orchestrator.**
   Review `.github/project-pulse-brief.md`, agent definitions, and the current
   `app/` and `.vscode/` state. Confirm there are no existing patterns or files
   that alter the planned scopes. Share the contract in section 3.

2. **Establish data values and visual hook guidance — Coder and Designer.**
   The Coder proposes the small fixed vocabulary for statuses and priorities,
   the `summary` field, and representative project records. The Designer
   provides the HTML-facing class/hook guidance, including `.dashboard`,
   `.project-card`, and any modifiers needed for status and priority. The
   Orchestrator reconciles names once, before file work begins.

3. **Run independent implementation tasks in parallel after contract approval.**
   - The **Designer** creates `app/styles.css` with the complete responsive
     design system and styling rules.
   - The **Coder** independently creates `app/project-data.json` and
     `.vscode/launch.json`.

   These tasks have distinct files, so neither needs to wait for the other
   after the shared contract is approved.

4. **Review parallel outputs — Orchestrator.**
   Check that the data is valid and uses the agreed vocabulary, the launch
   configuration is strict JSON and meets its exact values, and the stylesheet
   exposes the agreed hooks. Return discrepancies to the file owner; do not
   begin HTML work with an unresolved contract mismatch.

5. **Implement sequential HTML integration — Coder.**
   After **both** `app/project-data.json` is accepted and the Designer's hook
   guidance/styles are available, create `app/index.html`. HTML rendering waits
   for both the data contract and Designer hook guidance. Build a semantic page
   shell, fetch the JSON relative to the page, validate the response and
   top-level array, create one project card per project, and populate name,
   owner, status, recent activity, priority, and summary from the data. Apply
   the agreed hooks, including `.dashboard` and `.project-card`. Provide
   loading, empty-list, fetch, malformed-JSON, and invalid-record states with
   understandable text.

6. **Perform integrated review — Orchestrator, with focused feedback from
   Coder and Designer.**
   Serve the `app/` directory using the launch configuration, inspect the
   dashboard at the index URL, and complete the validation list below. Route
   fixes only to the owner of the affected file. Repeat focused checks after
   changes.

7. **Close out — Orchestrator.**
   Report files changed, validation results, unresolved risk (if any), and
   clear confirmation that no git staging, commit, or push was performed.

## 5. Dependencies and work sequencing

| Work item | Depends on | Why |
| --- | --- | --- |
| Shared data/display contract | Brief and agent requirements | It fixes field names, allowed display values, and hooks before independent work. |
| `app/project-data.json` | Shared contract | Data must have the required top-level `projects` array and agreed values. |
| `.vscode/launch.json` | Brief requirements | It is independent of UI code once its required command, directory, and URL are known. |
| `app/styles.css` | Shared hook/data-display contract | Status/priority treatment must correspond to the values HTML will expose. |
| `app/index.html` | **Both** accepted data contract/data file **and** Designer hook guidance/styles | Rendering requires exact data names and stable classes to avoid duplicated or incompatible markup. |
| Final visual and runtime review | All four deliverables | Rendering, CSS, JSON, and server behavior only prove cohesive together. |

### Parallel-work decision

Use a short shared-contract phase followed by controlled parallelism:

- **Parallel:** the Designer owns and edits `app/styles.css`; meanwhile the
  Coder can independently create `app/project-data.json` and
  `.vscode/launch.json`.
- **Sequential:** HTML is not parallel work. The Coder begins `app/index.html`
  only after the data contract/data output and Designer hook guidance are
  available and reconciled.
- **Final:** the Orchestrator performs one cohesive integration and launch
  review after HTML is complete.

This minimizes idle time without allowing overlapping edits or a markup/CSS
contract drift.

## 6. Edge cases and risks

- Opening `index.html` directly via `file://` can prevent `fetch()` from
  loading JSON. Validation and learner guidance must use the HTTP server.
- A server started in the repository root could show a directory listing or
  resolve the wrong paths. The launch configuration must use
  `${workspaceFolder}/app` and open `/index.html`.
- Invalid JSON, a missing `projects` key, a non-array `projects` value, an
  empty array, unavailable JSON, or a network/HTTP failure must not leave a
  blank dashboard. The page needs visible loading, empty, and error feedback.
- Incomplete project records or non-string required fields can create
  misleading cards. Validate required fields before rendering and describe the
  problem explicitly.
- Status/priority labels that differ in capitalization or vocabulary from CSS
  selectors can silently lose their visual treatment. Normalize/classify values
  deterministically and keep the contract finite.
- Project names, owners, summaries, and activity text are data, not trusted
  markup. Insert them with text APIs rather than HTML interpolation.
- Long names, long recent-activity messages, and narrow mobile screens may
  overflow or make cards hard to scan. CSS should allow wrapping and use a
  responsive grid/stack without horizontal scrolling.
- Color-only status or priority indicators exclude some users. Pair color with
  visible text, sufficient contrast, and semantic labels.
- Keyboard focus visibility, heading order, and readable content order can be
  lost in a card-first design. Preserve semantics and visible focus treatment.
- `python3` or VS Code debugging support may not be available in every learner
  environment. The launch review should surface such environmental failures
  separately from application failures.

## 7. Concrete validation expectations

### Required files and syntax

- Confirm all four required files exist at their exact paths.
- Parse `app/project-data.json` with a JSON parser. Verify the root has a
  `projects` array and every record has non-empty `name`, `owner`, `status`,
  `recentActivity`, and `priority` fields, plus the agreed `summary`.
- Parse `.vscode/launch.json` as strict JSON: no comments or trailing syntax.
  Confirm the configuration name is exactly `Run Project Pulse Dashboard`,
  `cwd` is exactly `${workspaceFolder}/app`, the command is exactly
  `python3 -m http.server 5500`, and the server-ready URL is exactly
  `http://localhost:%s/index.html`.
- Check that `app/index.html` references/loads `styles.css` and fetches
  `project-data.json` using a path that works when served from `app/`.

### Data-driven behavior

- Start with valid JSON and verify that the number of rendered
  `.project-card` elements equals `projects.length`.
- Verify every card displays the data-provided name, owner, status, recent
  activity, priority, and summary; no project content should be duplicated as
  hard-coded card markup.
- Test the loading state and simulate or temporarily inspect handling for an
  unavailable/malformed data response, missing/non-array `projects`, an empty
  array, and a record missing a required field. Each condition should have
  clear feedback and no uncaught browser-console error.
- Confirm status badges and priority treatment correspond to actual data
  values and include text labels, not color alone.

### Styling, responsive behavior, and accessibility

- Confirm the final DOM includes and stylesheet styles the deterministic
  `.dashboard` and `.project-card` hooks.
- Visually inspect that cards are clearly separated, status badges are visible,
  priorities are distinguishable, spacing and typography are readable, and
  the stylesheet uses both `border-radius` and `box-shadow`.
- Check a narrow mobile viewport and a wider desktop viewport. Cards should
  reflow cleanly, text should wrap, controls/content should remain usable, and
  there should be no unintended horizontal scroll.
- Verify a meaningful page title and heading hierarchy, semantic regions and
  list/article/card structure as appropriate, adequate foreground/background
  contrast, visible keyboard focus, and no information conveyed by color
  alone.

### Launch preview

- Launch the VS Code configuration and ensure it serves from `app/` on port
  5500.
- Confirm the ready action opens `http://localhost:%s/index.html` and that the
  browser displays Project Pulse immediately rather than a directory listing.
- Load the page through the server, inspect for successful JSON retrieval and
  absence of console errors, then stop the local server after validation.

## 8. Orchestrator handoff and integration process

1. Give each specialist the exact file scope from the ownership table and the
   shared contract. State explicitly that agents must not stage, commit, or
   push.
2. Collect the Coder's data vocabulary/sample-data and launch-config handoff
   plus the Designer's hook/design handoff. Compare them against the brief
   before authorizing HTML.
3. If a contract conflict appears, ask only the owner of the conflicting file
   to resolve it. Do not ask two agents to edit the same file.
4. Assign `app/index.html` to the Coder with the accepted JSON schema and
   exact class-hook list. Require its handoff to name rendering, error-state,
   and runtime checks completed.
5. Conduct integration review in the order: JSON validity, launch configuration
   validity, HTML data rendering, CSS hook alignment, responsive/accessibility
   inspection, then live launch preview.
6. Return precise findings to the relevant owner for a focused correction;
   rerun only the affected checks plus the launch smoke test.
7. Provide the learner a concise final outcome: delivered files, successful
   validation, any environmental limitation or remaining risk, and confirmation
   that git operations remain under learner control.

## 9. Open questions

No blocking product questions remain in the current brief. During the shared
contract phase, the Orchestrator should choose and record the exact finite
status and priority labels (and any related CSS modifier naming) before the
Designer and Coder begin their separate files. If the existing repository gains
an app pattern before implementation begins, the Orchestrator should reassess
this plan against that pattern rather than silently diverging from it.
