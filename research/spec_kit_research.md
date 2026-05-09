#generated-content-danger-will-robinson

     1|# Research Findings: `spec-kit` Implementation Patterns and Python Integration
     2|
     3|## Overview
     4|`spec-kit` is a toolkit designed to facilitate **Spec-driven Development (SDD)**. While many guides focus on Gemini or Claude, it is specifically compatible with **OpenCode**.
     5|
     6|## OpenCode Integration Patterns
     7|
     8|### 1. Initialization with OpenCode
     9|You can initialize a project with OpenCode support using `uvx` from the `spec-kit` repository:
    10|```bash
    11|uvx --from git+https://github.com/github/spec-kit.git specify init <PROJECT_NAME> --ai opencode --here
    12|```
    13|*(Note: The `--ai opencode` flag is the key to ensuring OpenCode-specific templates and command structures are used.)*
    14|
    15|### 2. OpenCode-Specific Setup (Migration/Configuration)
    16|If you are migrating a project or using an existing Claude/Gemini setup, you should:
    17|1.  **Move Command Folders**: Move `.claude/commands` to `.opencode/commands`.
    18|2.  **Remove Legacy Folders**: Delete the empty `.claude` directory.
    19|3.  **Update References**: Search `.opencode` and `.specify` directories for any reference to "claude" and update them to "opencode".
    20|4.  **Update Memory File**: Rename `CLAUDE.md` to `AGENTS.md` (OpenCode's convention for session memory).
    21|
    22|### 3. OpenCode Slash Commands
    23|Once integrated, you can use specific slash commands within the OpenCode chat interface to drive the SDD workflow:
    24|*   `**/specify**`: Generates a `spec.md` file containing user stories, acceptance scenarios, and requirements.
    25|*   `**/plan**`: Generates technical plan files including `plan.md`, `data-model.md`, `quickstart.md`, and `research.md`.
    26|*   `**/tasks**`: Breaks the plan into an actionable `tasks.md` file.
    27|*   `**/implement**`: Instructs OpenCode to work through the `tasks.md` file, creating code, running commands, and updating progress.
    28|
    29|## Implementation Details for Python
    30|
    31|### 1. Core Workflow (The 5 Stages of SDD)
    32|1.  **`/speckit.constitution`**: Establines project governing principles.
    33|2.  **`/speckit.specify`**: Defines requirements and user stories.
    34|3.  **`/speckit.plan`**: Creates a technical implementation plan.
    35|4.  **`/speckit.tasks`**: Breaks the plan into an actionable task list.
    36|5.  **`/speckit.implement`**: Executes the tasks.
    37|
    38|### 2. Agent Integrations (Python Pattern)
    39|In OpenCode, integrations are handled by customizing the command structures within `.opencode/commands`. The agent uses these commands to interact with the `specify` CLI.
    40|
    41|### 3. Requirements and Environment
    42|*   **Python Version**: 3.11+ is required.
    43|*   **Package Management**: `uv` is the recommended tool.
    44|*   **Environment Configuration**: Use a `.env` file within the project or agent directory for any necessary API keys.
    45|
    46|## Summary Table of Commands
    47|
    48|| Command | Purpose |
    49|| :--- | :--- |
    50|| `/speckit.constitution` | Establish project principles |
    51|| `/speckit.specify` | Define requirements/user stories |
    52|| `/speckit.plan` | Create technical implementation plans |
    53|| `/speckit.tasks` | Generate actionable task lists |
    54|| `/speckit.implement` | Execute tasks and build features |
    55|| `/speckit.taskstoissues` | Convert tasks into GitHub issues |
    56|
    57|