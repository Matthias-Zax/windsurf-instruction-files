# 07 — CI / CD

Start with GitHub Actions (see `templates/.github/workflows/playwright.yml`).

Highlights:

- Matrix on OS/node only if needed; keep it simple initially
- Cache npm dependencies
- Install Playwright browsers (`npx playwright install`)
- Publish HTML report and traces on failure
- Pass environment variables via repository secrets (e.g., `BASE_URL`)

For Azure DevOps or other CI, mirror these steps: checkout, Node setup, `npm ci`, `npx playwright install`, `npx playwright test`, upload artifacts.
