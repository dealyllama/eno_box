# OpenCode Agent Instructions: eno_box

## 🚀 Core Commands
Always use `uv run` for all commands to ensure execution within the project's virtual environment.
*   **Linting**: `uv run ruff check .`
*   **Formatting**: `uv run ruff format .`
*   **Testing**: `uv run pytest`
*   **Dependency Management**: `uv add <package>` or `uv remove <package>`

## 🏗️ Project Architecture
*   **Package Root**: `src/eno_box/` (The package name follows the `src` layout).
*   **Test Suite**: `tests/` directory, mirroring the `src/` structure.
*   **Tooling**: All development tools (linting, testing, type-checking) are managed via `uv` and configured in `pyint.toml`.

## 🛠️ Workflow Conventions
*   **Lint -> Test**: Always run `ruff check` and `pytest` before considering a task complete.
*   **Spec-Driven Development**: Use `spec-kit` slash commands (`/speckit.specify`, `/speckit.plan`, etc.) to drive feature implementation.
*   **Dependency Groups**: All dev/test tools are located in the `[dependency-groups].dev` section of `pyproject.toml`.

## ⚠️ Critical Constraints
*   **NEVER** use `python`, `pip`, or `pytest` directly. **ALWAYS** use `uv run <command>`.
*   **NEVER** manually activate the virtual environment; rely on `uv run`.
*   **Always** follow the `src/` layout pattern when creating new modules or test files.
*   **Always** include the `#generated-content-danger-will-robinson` tag in all generated documentation (`.md` files) and git commit messages for non-source-code changes.
