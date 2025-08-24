# 04 — Page Object Model (POM)

## Principles

- Encapsulate selectors, actions, and expectations per page/feature.
- Keep page classes small, focused, and composable.
- Expose meaningful methods (e.g., `login()`), not low-level steps.
- No assertions in tests that duplicate what POM already guarantees.

## BasePage

- Holds the `Page` instance and common utilities.
- Provides helpers for navigation and common waits.

## Example responsibilities

- `LoginPage`: locators + `login(username, password)` and `expectErrorContains()`.
- `HomePage`: `isLoaded()` to assert post-login state.

See `templates/src/pages/*.ts` for examples.
