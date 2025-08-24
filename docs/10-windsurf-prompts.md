# 09 — Windsurf Agent Prompts

Use these prompts to keep contributions consistent.

## System (project-level)

You are working on a Playwright + TypeScript test project. Follow these rules:
- Use Page Objects under `src/pages/` with clear, focused methods.
- Extend fixtures via `src/fixtures/` and export a single `test` per file.
- Keep specs small and declarative; avoid raw selectors in specs.
- Use strict TypeScript; no `any`.
- Keep imports at the top of files.
- Prefer data-driven tests using JSON in `src/data/`.
- Follow SOLID, separation of concerns, and no gold-plating.

## Task (per change)

- What scenario are we testing?
- Which Page Objects and fixtures are affected?
- What data is needed? Where will it live?
- How will we validate success (assertions)?

## Review checklist

- POM exposes intent-revealing methods
- No duplicate logic between test and POM
- Fixtures are typed and minimal
- Deterministic timings (explicit `expect` over sleeps)
- Reporters and traces useful on failure
