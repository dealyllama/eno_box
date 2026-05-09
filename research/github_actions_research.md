# Research Findings: GitHub Actions for Python CI/CD

## Overview
GitHub Actions is the industry standard for automating CI/CD for Python projects. It allows for automated linting, testing, and deployment on every push or pull request.

## Key Workflow Components

### 1. Workflow Triggers
*   **`on: [push, pull_request]`**: Ensures all code changes are validated before merging.
*   **`workflow_dispatch`**: Allows manual triggering of the workflow for ad-hoc runs (e.g., after manual environment changes).

### 2. Core Implementation Strategies

#### A. Linting with `ruff`
*   **Annotation**: Use `ruff check --format=github .` to enable **GitHub Annotations**. This integrates errors directly into the PR UI, making them highly visible to developers.
*   **Formatting**: Use `ruff format --check .` to ensure that all code adheres to the project's established style guidelines.
*   **Performance**: `ruff` is extremely fast, making it ideal for CI environments where minimizing build time is critical.

#### B. Testing with `pytest`
*   **Matrix Testing**: Use a `strategy: matrix` approach to run tests against multiple Python versions (e.s. 3.12, 3.13) simultaneously. This ensures the project remains compatible across the supported version range.
*   **Coverage**: Integrate `pytest-cov` to monitor code coverage. It is recommended to set a failure threshold (e.g., `--cov-fail-under=80`) to prevent coverage erosion.
*   **Dependency Management**: While `pip` is common in CI, using `uv` is preferred for consistency with our local development environment.

#### C. Environment & Caching
*   **`actions/setup-python`**: The standard action for configuring the Python runtime.
*   **Caching**: Utilize the `cache: 'pip'` or `cache: 'uv'` option within `setup-python` to cache dependencies, significantly reducing build times on subsequent runs.

### 3. Recommended Pipeline Structure

1.  **Checkout**: Use `actions/checkout@v4` to pull the repository into the runner.
2.  **Setup**: Use `actions/setup-python` to configure the target Python version.
3.  **Install**: Use `uv sync` or `uv run` to install all dependencies from the `pyproject.toml`.
4.  **Lint**: Run `ruff check` and `ruff format --check`.
5.  **Test**: Run `pytest` with coverage enabled.

## Summary Table

| Feature | Implementation | Benefit |
| :--- | :--- | :--- |
| **Linting** | `ruff check --format=github` | Direct PR annotations |
| **Testing** | `pytest` + matrix strategy | Cross-version compatibility |
| **Coverage** | `pytest-cov` | Prevents coverage regression |
| **Speed** | `uv` + `actions/setup-python` caching | Faster, more reliable builds |
