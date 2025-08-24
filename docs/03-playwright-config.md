# 03 — Playwright Config

A solid `playwright.config.ts` enables parallel, deterministic tests with good diagnostics.

Key choices in the template:

- `retries`: `2` in CI, `0` locally
- `workers`: `1` in CI for stability; parallel locally
- `trace`: `on-first-retry` (keeps artifacts lean)
- `video`: `retain-on-failure`
- `screenshot`: `only-on-failure`
- `reporter`: `html` + `list`
- `projects`: Chromium, Firefox, WebKit desktop presets

Environment handling:

- `baseURL` read from `process.env.BASE_URL` with a sensible default
- Add secrets as CI env vars or `.env` (never commit secrets)

See `templates/playwright.config.ts` for the full example.
