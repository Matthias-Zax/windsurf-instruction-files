# 09 — Test Case Naming

This guide defines a consistent, searchable naming scheme for Playwright test titles. It improves readability, filtering, and reporting across teams and CI.

## Naming Pattern

Use exactly four parts separated by underscores:

```
<Type>_<MODULE>_<action>_<assertion>
```

Example:

```
PT_LOGIN_loginWithValidCredentials_userIsLoggedIn
```

### Components
- **Type (prefix)**: encodes the test intent
  - `PT` — Positive test case (happy path)
  - `NT` — Negative test case (invalid input/denied actions)
  - `ET` — Exception test case (system/technical errors, e.g., timeouts)
  - `MT` — Misuse test case (abuse/unsupported usage/security)
  - `AT` — Alternative test case (alternate but valid flows)

- **MODULE**: the product area in UPPER_SNAKE or UPPER token
  - Examples: `LOGIN`, `CHECKOUT`, `USER_PROFILE`, `SEARCH`
  - Keep it short and stable over time.

- **action**: lowerCamelCase, imperative verb phrase that states what the test does
  - Examples: `loginWithValidCredentials`, `searchForQuery`, `checkoutWithSavedCard`

- **assertion**: lowerCamelCase, states the primary verification
  - Examples: `userIsLoggedIn`, `resultsContainAI`, `errorIsShown`

## Rules of Thumb
- **One primary assertion phrase**: keep the final segment focused on the main expected outcome.
- **Use verbs** in the action segment; **use outcomes** in the assertion segment.
- **Avoid ambiguity**: prefer `errorIsShown` over `fails`.
- **Keep titles concise**: target under ~100 characters; omit redundant words.
- **Characters**: `[A–Z0–9_]` for `MODULE`, `[a–zA–Z0–9]` for camelCase segments.

## Examples
- Positive
  - `PT_LOGIN_loginWithValidCredentials_userIsLoggedIn`
  - `PT_SEARCH_searchForTestAutomation_firstResultContainsAI`
- Negative
  - `NT_LOGIN_loginWithInvalidPassword_errorIsShown`
  - `NT_CHECKOUT_payWithExpiredCard_paymentIsDeclined`
- Exception
  - `ET_CHECKOUT_checkoutWhenGatewayTimesOut_gracefulFallbackIsApplied`
- Misuse
  - `MT_ACCOUNT_updateEmailWithSqlInjection_payloadIsRejected`
- Alternative
  - `AT_PROFILE_updateAddressWithoutPhone_updateIsSaved`

## Data-Driven Variants (optional)
When the same scenario runs with multiple datasets, append a dataset suffix using a double underscore:

```
PT_LOGIN_loginWithRole_userSeesRoleBanner__admin
PT_LOGIN_loginWithRole_userSeesRoleBanner__viewer
```

- Use a short, lowercase, hyphenless slug for the dataset (`admin`, `de-DE`, `slow-network` → prefer `slowNetwork`).
- Keep the core four-part structure intact; only vary the dataset suffix.

## Validation Regex
Use this to validate titles in tooling (linters, reporters):

- **Core pattern (no dataset):**
```
^(PT|NT|ET|MT|AT)_[A-Z][A-Z0-9_]*_[a-z][A-Za-z0-9]*_[a-z][A-Za-z0-9]*$
```
- **With optional dataset suffix:**
```
^(PT|NT|ET|MT|AT)_[A-Z][A-Z0-9_]*_[a-z][A-Za-z0-9]*_[a-z][A-Za-z0-9]*(?:__[a-z0-9]+)?$
```

## Playwright Usage
- **Spec file name**: `<module>.spec.ts` (e.g., `login.spec.ts`, `search.spec.ts`).
- **Describe block**: group by module for readability.
- **Test title**: follow the pattern exactly.

```ts
// tests/e2e/login.spec.ts
import { test, expect } from '../../src/fixtures/test-base';

test.describe('LOGIN', () => {
  test('PT_LOGIN_loginWithValidCredentials_userIsLoggedIn', async ({ loginPage, homePage }) => {
    await loginPage.open();
    await loginPage.login('john', 'secret');
    await homePage.isLoaded();
  });

  test('NT_LOGIN_loginWithInvalidPassword_errorIsShown', async ({ loginPage }) => {
    await loginPage.open();
    await loginPage.login('john', 'wrong');
    await loginPage.expectErrorContains('Invalid');
  });
});
```

## Do / Don’t
- Do **use imperative verbs**: `login`, `search`, `checkout`.
- Do **encode the main outcome**: `userIsLoggedIn`, `errorIsShown`.
- Do **keep modules stable** over time; rename cautiously.
- Don’t include environment or build info in the title (use tags/metadata instead).
- Don’t pack multiple outcomes into the assertion segment.

## Rationale
- Enables consistent filtering, search, and flakiness tracking in CI.
- Makes reports self-explanatory for engineers, QA, and stakeholders.
- Encourages single-responsibility tests with clear intent and outcome.
