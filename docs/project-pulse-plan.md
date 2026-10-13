# Project Pulse implementation plan

## Summary

Build a polished, accessible static dashboard that lets contributors quickly see
which projects are active, who owns them, their status, recent activity, and
priority or risk. The app uses the project data in a JSON file and opens directly
to its dashboard through the VS Code **Run Project Pulse Dashboard**
configuration. Follow the requirements in `.github/project-pulse-brief.md`.

No application framework or package installation is needed. Serve the `app/`
directory over HTTP so the page can fetch `project-data.json`; opening the HTML
directly as a `file://` URL will not reliably load JSON.

## Phases and file assignments

### 1. Agree on the interface contracts

- **Planner / Orchestrator:** Use the fixed project schema from the brief:
  `projects` is a top-level array, and each entry has `name`, `owner`,
  `status`, `recentActivity`, and `priority`.
- **Designer:** Define a clear dashboard hierarchy and accessible visual
  treatment. The shared CSS/markup contract includes `.dashboard` and
  `.project-card`; status and priority must remain understandable without color.
- **Coder:** Follow that data and selector contract when implementing the page.
- **Files:** No application files; this phase sets the contracts that prevent
  parallel work from diverging.

### 2. Build independent assets in parallel

- **Designer — `app/styles.css`:** Create the responsive dashboard presentation,
  including readable spacing and typography, visible project cards, clear
  status/priority treatments, and the `.dashboard` and `.project-card`
  selectors. Use rounded corners and shadows as visual affordances, while
  preserving contrast, keyboard focus visibility, and reduced-motion
  preferences if transitions are used.
- **Coder — `app/project-data.json`:** Supply a non-empty set of realistic,
  contributor-friendly project examples. Keep the top-level `projects` array
  and required fields consistent across entries.
- **Coder — `.vscode/launch.json`:** Add strict JSON for the **Run Project Pulse
  Dashboard** launch configuration. Serve from `${workspaceFolder}/app` using
  `python3 -m http.server 5500`, and configure `serverReadyAction` to open
  `http://localhost:%s/index.html`, not the server's directory root.

### 3. Connect the dashboard

- **Coder — `app/index.html`:** Implement the semantic page with the exact
  document title `Project Pulse`, a link to `styles.css`, and client-side
  loading of `project-data.json`. Render one `.project-card` per project and
  visibly include its name, owner, status, recent activity, and priority.
  Provide an understandable loading and data-load error state rather than
  silently presenting an empty dashboard.
- **Dependency:** The page consumes the agreed JSON schema and CSS class
  contract from Phase 1. It can be authored alongside Phase 2 assets because
  those interfaces are fixed; final integration depends on all assets existing.

### 4. Integrate and verify

- **Coder:** Resolve implementation issues within assigned files and report
  changes and any remaining limitations.
- **Orchestrator:** Review the integrated result across all four files and
  confirm the run configuration, markup, styles, and data agree.
- **Files:** Validation may require focused fixes in `app/index.html`,
  `app/styles.css`, `app/project-data.json`, or `.vscode/launch.json`, with the
  original owner making changes to avoid conflicting edits.

## Responsibilities

- **Designer:** Own the dashboard's information hierarchy, visual design,
  responsive behavior, and accessibility guidance; implement those decisions
  in `app/styles.css` only.
- **Coder:** Own application and launch implementation in `app/index.html`,
  `app/project-data.json`, and `.vscode/launch.json`; integrate the Designer's
  styling through the shared selector contract and validate the runnable app.
- **Planner:** Define phases, file ownership, dependencies, parallel work, edge
  cases, and measurable validation expectations before implementation.
- **Orchestrator:** Delegate scoped work, communicate the shared contracts,
  keep overlapping edits separate, integrate results, and report validation.

## Dependencies and parallel work decisions

- The project schema and shared selectors are agreed before implementation; this
  is the main coordination dependency.
- In Phase 2, Designer can work on `app/styles.css` in parallel with Coder
  working on `app/project-data.json` and `.vscode/launch.json`. These assignments
  do not overlap.
- Coder can begin `app/index.html` in parallel once the Phase 1 interface
  contracts are fixed. The HTML integration and end-to-end validation depend on
  the data, stylesheet, and launch configuration being available.
- Final browser verification is sequential after all four assigned files are
  present. Keep ownership exclusive; route any cross-file correction to the
  assigned file owner.

## Edge cases and risks

- JSON fetch may fail when the page is opened directly from disk, when the file
  is missing or invalid, or when the server is not running. Run through the
  provided HTTP launch configuration and show a useful page-level error if the
  fetch or JSON parsing fails.
- A missing or empty `projects` array should not cause a script exception or
  imply that projects exist; show an explicit empty state.
- Render JSON-provided text as text, not injected HTML. Ensure long names,
  activity descriptions, and labels wrap on narrow screens.
- Status and priority meaning must be available in text and not conveyed through
  color alone. Preserve semantic reading order and visible keyboard focus.
- The launch configuration must be valid JSON, use the `app/` working
  directory, and open `index.html` after the server is ready; opening the
  directory URL would show a file listing instead.

## Validation expectations

1. Confirm all assigned files exist and `.vscode/launch.json` parses as strict
   JSON.
2. Parse `app/project-data.json` as JSON; verify the top-level `projects` value
   is an array and each entry contains `name`, `owner`, `status`,
   `recentActivity`, and `priority`.
3. Inspect `app/index.html` for the exact title `Project Pulse`, its stylesheet
   link and JSON reference, semantic structure, `.project-card` rendering, and
   visible output for all required project fields. Check loading, empty, and
   fetch/parse error states.
4. Inspect `app/styles.css` for `.dashboard`, `.project-card`, responsive
   behavior, readable spacing, adequate contrast, and the required card
   treatment (`border-radius` and `box-shadow`).
5. Run **Run Project Pulse Dashboard**. Confirm it serves from `app/`, opens
   `http://localhost:<port>/index.html` rather than a directory listing, loads
   the JSON, and displays the project cards. Check a narrow viewport and basic
   keyboard navigation/focus visibility.
6. No automated application test suite or dependencies currently exist in the
   repository; use the structural checks above and a live browser preview for
   end-to-end verification.

## Open questions

- None block implementation. The brief does not prescribe exact project
  examples or a particular color palette, so Designer and Coder may choose
  suitable accessible values and representative sample data.
