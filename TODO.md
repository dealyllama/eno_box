# Project Scaffolding: eno_box

## Phase 0: Research & Discovery
- [x] Research `spec-kit` implementation patterns and Python integration
- [x] Research useful `opencode` skills and agents for this particular project
- [x] Research modern `pyproject.toml` configuration for `ruff` and `pytest`
- [x] Research FM synthesis (see `research/fm_synthesis_research_plan.md`)
- [ ] Research wavetable synthesis (see `research/wavetable_synthesis_research_plan.md`)
- [ ] Research python / C++ conventions (see `research/python_to_cpp_convention_plan.md`)
- [ ] Research spec kit best practices (<<REPLACE WITH RESEARCH PLAN LINK>>)
- [ ] decide if we should use spec kit approaches going forward to manage research tasks etc
- [ ] Research CrewAI integration 

## Phase 1: Foundation
- [x] Initialize Git repository
- [x] Push repository to GitHub using gh
- [x] Create `.gitignore` (Python & `.eno_box_venv` specific)
- [x] Run `opencode /init` to update `AGENTS.md`
- [ ] add to section in agents.MD specifying that assertions in unit test must be written by a human and that agents.md may not be updated by an agent although an agent may suggest improvements.
- [ ] Set up project environment using `uv`
- [ ] Install dependencies via `uv add`: `ruff`, `pytest`, `spec-kit`

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
