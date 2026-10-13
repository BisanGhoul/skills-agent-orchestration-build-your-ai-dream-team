# Agent team

I will use GitHub Copilot CLI in a Codespace to orchestrate this custom agent team as we build Mona's Project Pulse dashboard.

| Agent | Model | Responsibility | Definition |
|---|---|---|---|
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the specialists: gets the plan, divides work into phases with clear file ownership and dependencies, delegates tasks, and verifies the integrated result. It does not implement the dashboard itself. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository, documentation, dependencies, and edge cases, then produces an actionable implementation plan with file assignments, sequencing, parallel work, and validation expectations. It does not write code. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements the dashboard and assigned runnable-app support, following the plan and existing project patterns; validates the changes and reports any remaining risks. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Shapes the dashboard's user experience, accessibility, information hierarchy, interaction flow, responsive layout, and visual design. | `.github/agents/designer.agent.md` |

The Planner researches and proposes the implementation steps. The Orchestrator turns those into scoped phases, assigning the Designer and Coder clear file ownership and sequencing overlapping or dependent work while allowing independent tasks to run in parallel. The Designer defines the dashboard experience; the Coder implements the assigned design and application work. The Orchestrator then checks that the pieces integrate and reports the result.
