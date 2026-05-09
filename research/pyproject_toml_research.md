# Research Findings: `pyproject.toml` Configuration for `ruff` and `pytest`

## Overview
`pyproject.toml` has become the single source of truth for Python project configuration, replacing multiple files like `setup.py`, `setup.cfg`, and `requirements.txt`. Modern tooling (especially `uv`, `ruff`, and `pytest`) leverages this file for metadata, dependency management, and tool-specific settings.

## Key Configuration Patterns

### 1. Dependency Management (using `uv`)
Modern Python development favors `uv` for its speed and deterministic lockfile (`uv.lock`).
*   **Runtime Dependencies**: Defined in the `[project]` section under `dependencies`.
*   **Development Dependencies**: Use the specialized `[dependency-groups]` (PEP 735) for tools like `ruff`, `pytest`, and `mypy`. This is the modern, cleaner replacement for the legacy `[project.optional-dependencies].dev` pattern.
*   **Command usage**: Always use `uv add <pkg>` to update the file and `uv run <cmd>` to execute tools within the project environment.

### 2. `ruff` Configuration
`ruff` unifies the functionality of `flake8`, `black`, and `isort`.
*   **Target Version**: Set `target-version` (e.g., `"py312"`) to ensure compatibility.
*   **Linting Rules**: Use the `lint.select` array to enable specific rule sets (e.
    *   `E`, `F`, `W`: Basic pycodestyle and pyflakes.
    *   `I`: isort (sorting imports).
    *   `UP`: pyupgrade (modernizing syntax).
    *   `B`: bugbear (identifying common bugs).
*   **Formatting**: Configure `line-length` and `quote-style` under `[tool.ruff.format]`.
*   **Source Layout**: Explicitly define `src = ["src"]` to ensure `ruff` correctly identifies the package root.

### 3. `pytest` Configuration
Configure `pytest` under `[tool.pytest.ini_options]` (or `[tool.pytest]` in newer versions).
*   **Test Discovery**: Define `testpaths = ["tests"]` and `python_files = ["test_*.py"]`.
*   **Command Options**: Use `addopts` to enforce coverage and strictness.
    *   `--cov=src`: Automatically run coverage for the `src` directory.
    *   `--cov-fail-under=80`: Fail the build if coverage drops below 80%.
    *   `-ra`: Show extra summary info on test failures.

## Recommended `pyproject.toml` Template

```toml
[project]
name = "eno_box"
version = "0.1.0"
description = "Agentic coding framework project"
requires-python = ">=3.12"
dependencies = []

[dependency-groups]
dev = [
    "ruff>=0.8",
    "pytest>=8.0",
    "pytest-cov>=4.0",
]

[tool.ruff]
line-length = 100
target-version = "py312"
src = ["src"]

[tool.ruff.lint]
select = ["E", "F", "W", "I", "B", "UP", "S"]
ignore = ["E501"] # Line length handled by formatter

[tool.ruff.format]
quote-style = "double"

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "--cov=src --cov-report=term-missing --cov-fail-under=80"
```

## Summary of Best Practices
1.  **Single Source of Truth**: Keep all configs (linting, testing, metadata) in `pyproject.toml`.
2.  **Use `src/` Layout**: This prevents tests from accidentally importing the uninstalled local package, ensuring they run against the actual installed version.
3.  **Prefer `dependency-groups`**: Use the newer `[dependency-groups]` for all dev/test/lint tools.
4.  **Enforce Coverage**: Set a minimum coverage threshold in `pytest` configuration to prevent regression.
