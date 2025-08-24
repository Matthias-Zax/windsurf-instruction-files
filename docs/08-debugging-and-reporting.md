# 08 — Debugging and Reporting

## Built-ins

- HTML report: `npx playwright show-report`
- Trace viewer: `npx playwright show-trace test-results/<trace>.zip`
- Headed mode: `npm run test:headed`
- UI mode: `npm run test:ui`

## Useful settings

- `trace: 'on-first-retry'`
- `video: 'retain-on-failure'`
- `screenshot: 'only-on-failure'`

## Tips

- Use `test.step()` for logical grouping.
- Add custom assertions in POM methods where appropriate.
- Prefer stable selectors (data-testid) and explicit waits via `expect`.
