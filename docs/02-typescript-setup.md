# 02 — TypeScript Setup

Use strict TypeScript for maintainability and safety.

## Key settings (see `templates/tsconfig.json`)

- `"strict": true` — catches type issues early
- `"types": ["node", "@playwright/test"]` — enables Playwright globals
- `"resolveJsonModule": true` — import JSON data in specs/fixtures
- `"isolatedModules": true` — safer transpilation
- `"noEmit": true` — leave transpilation to Playwright and tooling

## Tips

- Prefer interfaces for object shapes.
- Export types for fixtures and data models.
- Avoid `any` — use `unknown` or proper generics.
