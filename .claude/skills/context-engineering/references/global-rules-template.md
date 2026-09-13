# Global Rules Template (CLAUDE.md)

Rules the assistant follows in every conversation on this project. Put this at the repo root as `CLAUDE.md`. The categories below are the durable part; the specifics should be rewritten for the project's actual language, tooling, and conventions.

## Project Awareness & Context

- Always read `PLANNING.md` at the start of a new conversation to understand architecture, goals, style, and constraints.
- Check `TASK.md` before starting a task. If the task is not listed, add it with a brief description and today's date.
- Use the naming conventions, file structure, and architecture patterns described in `PLANNING.md`.
- Use the project's virtual environment or toolchain for every command, including tests.

## Code Structure & Modularity

- Never create a file longer than 500 lines. Approaching the limit means it is time to split into modules.
- Organize code into clearly separated modules grouped by feature or responsibility.
- Use clear, consistent imports.
- Load configuration from environment variables rather than hardcoding it.

## Testing & Reliability

- Always create unit tests for new features (functions, classes, routes).
- After changing any logic, check whether existing tests need updating, and update them.
- Tests live in a `tests/` folder mirroring the main application structure.
- Each unit gets at least three tests: one expected use, one edge case, one failure case.

## Task Completion

- Mark tasks complete in `TASK.md` as soon as they are finished.
- Add sub-tasks and TODOs discovered mid-work to `TASK.md` under a "Discovered During Work" heading.

## Style & Conventions

- State the primary language and the formatter, linter, and type checker, with the exact commands.
- State the validation library, web framework, and ORM the project standardizes on.
- Write docstrings for every function in the project's documented style.

## Documentation & Explainability

- Update `README.md` when features are added, dependencies change, or setup steps change.
- Comment non-obvious code so a mid-level developer can follow it.
- For complex logic, add an inline comment explaining the why, not the what.

## AI Behavior Rules

- Never assume missing context. Ask when uncertain.
- Never hallucinate libraries or functions. Use only verified packages.
- Always confirm file paths and module names exist before referencing them in code or tests.
- Never delete or overwrite existing code unless explicitly instructed, or unless it is part of a task in `TASK.md`.

## Adapting This

Two failure modes to avoid. First, copying the template verbatim into a project whose stack it does not describe, which teaches the assistant the wrong conventions. Second, writing rules so broad they carry no information ("write good code"). Every rule should be specific enough that you could tell whether it was violated.
