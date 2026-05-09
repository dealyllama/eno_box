# Research Findings: `spec-kit` Implementation Patterns and Python Integration

## Overview
`spec-kit` is a toolkit designed to facilitate **Spec-driven Development (SDD)**. While many guides focus on Gemini or Claude, it is specifically compatible with **OpenCode**.

## OpenCode Integration Patterns

### 1. Initialization with OpenCode
You can initialize a project with OpenCode support using `uvx` from the `spec-kit` repository:
```bash
uvx --from git+https://github.com/github/spec-kit.git specify init <PROJECT_NAME> --ai opencode --here
```
*(Note: The `--ai opencode` flag is the key to ensuring OpenCode-specific templates and command structures are used.)*

### 2. OpenCode-Specific Setup (Migration/Configuration)
If you are migrating a project or using an existing Claude/Gemini setup, you should:
1.  **Move Command Folders**: Move `.claude/commands` to `.opencode/commands`.
2.  **Remove Legacy Folders**: Delete the empty `.claude` directory.
3.  **Update References**: Search `.opencode` and `.specify` directories for any reference to "claude" and update them to "opencode".
4.  **Update Memory File**: Rename `CLAUDE.md` to `AGENTS.md` (OpenCode's convention for session memory).

### 3. OpenCode Slash Commands
Once integrated, you can use specific slash commands within the OpenCode chat interface to drive the SDD workflow:
*   `**/specify**`: Generates a `spec.md` file containing user stories, acceptance scenarios, and requirements.
*   `**/plan**`: Generates technical plan files including `plan.md`, `data-model.md`, `quickstart.md`, and `research.md`.
*   `**/tasks**`: Breaks the plan into an actionable `tasks.md` file.
*   `**/implement**`: Instructs OpenCode to work through the `tasks.md` file, creating code, running commands, and updating progress.

## Implementation Details for Python

### 1. Core Workflow (The 5 Stages of SDD)
1.  **`/speckit.constitution`**: Establines project governing principles.
2.  **`/speckit.specify`**: Defines requirements and user stories.
3.  **`/speckit.plan`**: Creates a technical implementation plan.
4.  **`/speckit.tasks`**: Breaks the plan into an actionable task list.
5.  **`/speckit.implement`**: Executes the tasks.

### 2. Agent Integrations (Python Pattern)
In OpenCode, integrations are handled by customizing the command structures within `.opencode/commands`. The agent uses these commands to interact with the `specify` CLI.

### 3. Requirements and Environment
*   **Python Version**: 3.11+ is required.
*   **Package Management**: `uv` is the recommended tool.
*   **Environment Configuration**: Use a `.env` file within the project or agent directory for any necessary API keys.

## Summary Table of Commands

| Command | Purpose |
| :--- | :--- |
| `/speckit.constitution` | Establish project principles |
| `/speckit.specify` | Define requirements/user stories |
| `/speckit.plan` | Create technical implementation plans |
| `/speckit.tasks` | Generate actionable task lists |
| `/speckit.implement` | Execute tasks and build features |
| `/speckit.taskstoissues` | Convert tasks into GitHub issues |

