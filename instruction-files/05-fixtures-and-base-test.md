# 05 — Fixtures and Base Test

Use a layered fixture approach:

- `test-base.ts` exports a `test` that injects POM instances and common helpers.
- Additional fixtures (e.g., `auth-fixture.ts`) extend that `test` in a chain.
- Specs import the most specific `test` they need.

```ts
// tests import from the deepest fixture they need
import { test, expect } from '../../src/fixtures/test-base';

// or with auth helpers
import { test, expect } from '../../src/fixtures/auth-fixture';
```

Benefits:

- Centralized wiring
- Strong typing for fixtures
- Composability

See `templates/src/fixtures/*.ts`.
