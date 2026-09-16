# Project Pulse agent team

| Agent | Model | Responsibility | Definition |
| --- | --- | --- | --- |
| **Orchestrator** | Claude Opus 4.7 (copilot) | Coordinates the Project Pulse work, turns the plan into phases, assigns non-conflicting file scopes, and verifies the integrated dashboard. | `.github/agents/orchestrator.agent.md` |
| **Planner** | Claude Opus 4.7 (copilot) | Researches the repository and requirements, identifies dependencies and edge cases, and produces an ordered implementation and validation plan. | `.github/agents/planner.agent.md` |
| **Coder** | GPT-5.5 (copilot) | Implements dashboard behavior and runnable-app support with clear, deterministic, testable code. | `.github/agents/coder.agent.md` |
| **Designer** | Gemini 3.1 Pro (copilot) | Designs and implements the dashboard user experience, including accessible information hierarchy, responsive layouts, project cards, status badges, priority treatment, and styling. | `.github/agents/designer.agent.md` |

## How the team builds Project Pulse

The Orchestrator kicks off the project by asking the Planner to examine the repository and turn the Project Pulse requirements into a practical plan. The Planner hands back ordered steps, dependencies, edge cases, validation expectations, and explicit file ownership.

Using that plan, the Orchestrator assigns implementation work to the Coder and design work to the Designer. Independent tasks with separate file scopes can proceed in parallel; dependent or overlapping work is scheduled in sequence. Each specialist reports its changes and recommendations back to the Orchestrator, which uses those handoffs to coordinate the next phase.

The Coder delivers the dashboard functionality and any required runnable-app configuration, while the Designer delivers the polished, accessible interface. The Orchestrator then reviews the combined work to ensure the Project Pulse dashboard functions cohesively and presents a consistent final experience.
