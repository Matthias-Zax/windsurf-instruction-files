# 06 — Data-Driven Testing

Prefer JSON for structured test data; TypeScript can import JSON directly with `resolveJsonModule`.

## JSON example

```json
{
  "valid": [
    { "name": "Standard", "username": "standard_user", "password": "secret" }
  ],
  "invalid": [
    { "name": "Wrong password", "username": "standard_user", "password": "wrong" }
  ]
}
```

## Spec usage

```ts
import users from '../../src/data/users.json';

for (const u of users.valid) {
  test(`valid login: ${u.name}`, async ({ loginPage, homePage }) => {
    await loginPage.login(u.username, u.password);
    await homePage.isLoaded();
  });
}
```

Tips:

- Keep data realistic and minimal.
- Use factories/types for complex data.
- Avoid test inter-dependencies.
