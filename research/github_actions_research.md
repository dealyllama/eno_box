#generated-content-danger-will-robinson

     1|# Research Findings: GitHub Actions for Python CI/CD
     2|
     3|## Overview
     4|GitHub Actions is the industry standard for automating CI/CD for Python projects. It allows for automated linting, testing, and deployment on every push or pull request.
     5|
     6|## Key Workflow Components
     7|
     8|### 1. Workflow Triggers
     9|*   **`on: [push, pull_request]`**: Ensures all code changes are validated before merging.
    10|*   **`workflow_dispatch`**: Allows manual triggering of the workflow for ad-hoc runs (e.g., after manual environment changes).
    11|
    12|### 2. Core Implementation Strategies
    13|
    14|#### A. Linting with `ruff`
    15|*   **Annotation**: Use `ruff check --format=github .` to enable **GitHub Annotations**. This integrates errors directly into the PR UI, making them highly visible to developers.
    16|*   **Formatting**: Use `ruff format --check .` to ensure that all code adheres to the project's established style guidelines.
    17|*   **Performance**: `ruff` is extremely fast, making it ideal for CI environments where minimizing build time is critical.
    18|
    19|#### B. Testing with `pytest`
    20|*   **Matrix Testing**: Use a `strategy: matrix` approach to run tests against multiple Python versions (e.s. 3.12, 3.13) simultaneously. This ensures the project remains compatible across the supported version range.
    21|*   **Coverage**: Integrate `pytest-cov` to monitor code coverage. It is recommended to set a failure threshold (e.g., `--cov-fail-under=80`) to prevent coverage erosion.
    22|*   **Dependency Management**: While `pip` is common in CI, using `uv` is preferred for consistency with our local development environment.
    23|
    24|#### C. Environment & Caching
    25|*   **`actions/setup-python`**: The standard action for configuring the Python runtime.
    26|*   **Caching**: Utilize the `cache: 'pip'` or `cache: 'uv'` option within `setup-python` to cache dependencies, significantly reducing build times on subsequent runs.
    27|
    28|### 3. Recommended Pipeline Structure
    29|
    30|1.  **Checkout**: Use `actions/checkout@v4` to pull the repository into the runner.
    31|2.  **Setup**: Use `actions/setup-python` to configure the target Python version.
    32|3.  **Install**: Use `uv sync` or `uv run` to install all dependencies from the `pyproject.toml`.
    33|4.  **Lint**: Run `ruff check` and `ruff format --check`.
    34|5.  **Test**: Run `pytest` with coverage enabled.
    35|
    36|## Summary Table
    37|
    38|| Feature | Implementation | Benefit |
    39|| :--- | :--- | :--- |
    40|| **Linting** | `ruff check --format=github` | Direct PR annotations |
    41|| **Testing** | `pytest` + matrix strategy | Cross-version compatibility |
    42|| **Coverage** | `pytest-cov` | Prevents coverage regression |
    43|| **Speed** | `uv` + `actions/setup-python` caching | Faster, more reliable builds |
    44|