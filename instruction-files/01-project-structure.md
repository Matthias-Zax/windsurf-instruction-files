# 01 — Project Structure

Recommended layout (mirrored in `templates/`):

```
project-root/
  .github/workflows/playwright.yml     # CI pipeline (GitHub Actions)
  .env.example                         # Example env vars
  playwright.config.ts                 # Playwright configuration
  tsconfig.json                        # Strict TS config for tests
  src/
    pages/                             # Page Objects (POM)
      base-page.ts
      login-page.ts
      home-page.ts
    fixtures/                          # Typed fixtures, base test
      test-base.ts
      auth-fixture.ts
    data/                              # Test data (JSON/CSV)
      users.json
  tests/
    e2e/                               # Spec files
      login.spec.ts
      smoke.spec.ts
```

## Naming and grouping

- Page Objects end with `-page.ts` and encapsulate UI interactions.
- Fixtures live under `src/fixtures/` and export a single `test` to extend.
- Data files importable from TS need `resolveJsonModule: true`.
- Specs go under `tests/` and should be small and declarative.

## Responsibilities

- `Page Objects` — selectors, actions, and expectations for a page/feature.
- `Fixtures` — application-agnostic wiring (auth, data setup, POM instances).
- `Specs` — the business scenario using POM + fixtures; no raw selectors.
