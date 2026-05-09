# Project Scaffolding: eno_box

## Phase 0: Research & Discovery
- [x] Research `spec-kit` implementation patterns and Python integration
- [x] Research useful `opencode` skills and agents for this particular project
- [x] Research modern `pyproject.toml` configuration for `ruff` and `pytest`
- [x] Research best practices for GitHub Actions (linting/testing) in 2026

## Phase 1: Foundation
- [x] Initialize Git repository
- [ ] Push repository to GitHub using gh
- [x] Create `.gitignore` (Python & `.eno_box_venv` specific)
- [ ] Run `opencode /init` to update `AGENTS.md`
- [ ] Set up project environment using `uv`
- [ ] Install dependencies via `unv add`: `ruff`, `pytest`, `spec-kit`

## Phase 2: Project Structure & Configuration
- [ ] Create directory structure: `src/eno_box/` and `tests/`
- [ ] Initialize `pyproject.toml` with `[dependency-groups]` for dev tools
- [ ] Configure `spec-kit` agentic workflow integration

## Phase 3: Continuous Integration & Tooling
- [ ] Create `.github/workflows/lint.yml` (using `uv run ruff`)
- [ ] Create `.github/workflows/test.yml` (using `uv run pytest`)
- [ ] Create minimal executable entry point in `src/eno_box/`

## Phase 4: Verification & Documentation
- [ ] Create `README.md`
- [ ] Run `uv run ruff` to verify linting
- [ ] Run `uv run pytest` to verify testing environment
- [ ] Finalize `TODO.md`

