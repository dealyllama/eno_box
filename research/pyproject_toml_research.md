#generated-content-danger-will-robinson

     1|# Research Findings: `pyproject.toml` Configuration for `ruff` and `pytest`
     2|
     3|## Overview
     4|`pyproject.toml` has become the single source of truth for Python project configuration, replacing multiple files like `setup.py`, `setup.cfg`, and `requirements.txt`. Modern tooling (especially `uv`, `ruff`, and `pytest`) leverages this file for metadata, dependency management, and tool-specific settings.
     5|
     6|## Key Configuration Patterns
     7|
     8|### 1. Dependency Management (using `uv`)
     9|Modern Python development favors `uv` for its speed and deterministic lockfile (`uv.lock`).
    10|*   **Runtime Dependencies**: Defined in the `[project]` section under `dependencies`.
    11|*   **Development Dependencies**: Use the specialized `[dependency-groups]` (PEP 735) for tools like `ruff`, `pytest`, and `mypy`. This is the modern, cleaner replacement for the legacy `[project.optional-dependencies].dev` pattern.
    12|*   **Command usage**: Always use `uv add <pkg>` to update the file and `uv run <cmd>` to execute tools within the project environment.
    13|
    14|### 2. `ruff` Configuration
    15|`ruff` unifies the functionality of `flake8`, `black`, and `isort`.
    16|*   **Target Version**: Set `target-version` (e.g., `"py312"`) to ensure compatibility.
    17|*   **Linting Rules**: Use the `lint.select` array to enable specific rule sets (e.
    18|    *   `E`, `F`, `W`: Basic pycodestyle and pyflakes.
    19|    *   `I`: isort (sorting imports).
    20|    *   `UP`: pyupgrade (modernizing syntax).
    21|    *   `B`: bugbear (identifying common bugs).
    22|*   **Formatting**: Configure `line-length` and `quote-style` under `[tool.ruff.format]`.
    23|*   **Source Layout**: Explicitly define `src = ["src"]` to ensure `ruff` correctly identifies the package root.
    24|
    25|### 3. `pytest` Configuration
    26|Configure `pytest` under `[tool.pytest.ini_options]` (or `[tool.pytest]` in newer versions).
    27|*   **Test Discovery**: Define `testpaths = ["tests"]` and `python_files = ["test_*.py"]`.
    28|*   **Command Options**: Use `addopts` to enforce coverage and strictness.
    29|    *   `--cov=src`: Automatically run coverage for the `src` directory.
    30|    *   `--cov-fail-under=80`: Fail the build if coverage drops below 80%.
    31|    *   `-ra`: Show extra summary info on test failures.
    32|
    33|## Recommended `pyproject.toml` Template
    34|
    35|```toml
    36|[project]
    37|name = "eno_box"
    38|version = "0.1.0"
    39|description = "Agentic coding framework project"
    40|requires-python = ">=3.12"
    41|dependencies = []
    42|
    43|[dependency-groups]
    44|dev = [
    45|    "ruff>=0.8",
    46|    "pytest>=8.0",
    47|    "pytest-cov>=4.0",
    48|]
    49|
    50|[tool.ruff]
    51|line-length = 100
    52|target-version = "py312"
    53|src = ["src"]
    54|
    55|[tool.ruff.lint]
    56|select = ["E", "F", "W", "I", "B", "UP", "S"]
    57|ignore = ["E501"] # Line length handled by formatter
    58|
    59|[tool.ruff.format]
    60|quote-style = "double"
    61|
    62|[tool.pytest.ini_options]
    63|testpaths = ["tests"]
    64|addopts = "--cov=src --cov-report=term-missing --cov-fail-under=80"
    65|```
    66|
    67|## Summary of Best Practices
    68|1.  **Single Source of Truth**: Keep all configs (linting, testing, metadata) in `pyproject.toml`.
    69|2.  **Use `src/` Layout**: This prevents tests from accidentally importing the uninstalled local package, ensuring they run against the actual installed version.
    70|3.  **Prefer `dependency-groups`**: Use the newer `[dependency-groups]` for all dev/test/lint tools.
    71|4.  **Enforce Coverage**: Set a minimum coverage threshold in `pytest` configuration to prevent regression.
    72|