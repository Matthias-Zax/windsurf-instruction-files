# Playwright + TypeScript Central Instructions

This is a **central instruction repository** for standardized Playwright + TypeScript test automation across multiple projects. It provides reusable guidelines, templates, and best practices that can be referenced in any project's Windsurf instruction files.

## Architecture & Standards

- **Page Object Model (POM)** with typed fixtures
- **Test naming convention**: `<Type>_<MODULE>_<action>_<assertion>` (PT/NT/ET/MT/AT)
- **Data-driven testing** with JSON/CSV support
- **Strict TypeScript** configuration
- **CI-ready** Playwright setup with GitHub Actions
- **Security-first** credential management

## How to Use in Projects

### 1. Reference in Project Instructions
Add this to your project's Windsurf instruction files:
```markdown
# Testing Standards
Follow the Playwright + TypeScript guidelines from:
https://github.com/Matthias-Zax/windsurf-instruction-files

Key conventions:
- Test naming: PT_MODULE_action_assertion
- Page Object Model structure
- Credential management via .env (local) / GitHub Secrets (CI)
```

### 2. Copy Templates
Copy relevant files from `templates/` into your project:
```bash
# Core structure
cp -r templates/src/ your-project/
cp templates/playwright.config.ts your-project/
cp templates/.github/workflows/playwright.yml your-project/.github/workflows/

# Environment setup
cp templates/.env.example your-project/.env.example
```

### 3. Customize for Your App
- Update `src/pages/` with your application's selectors
- Modify `src/data/` with your test data
- Configure base URLs in `playwright.config.ts`
- Set up credentials in `.env` (never commit passwords)

## Security Guidelines

### Credential Management
- **Local development**: Use `.env` file (add to `.gitignore`)
- **CI/CD**: Use GitHub Secrets, never hardcode passwords
- **Test data**: Use non-production accounts with minimal privileges

### Example .env structure:
```bash
# .env (local only - never commit)
TEST_USER_EMAIL=test@example.com
TEST_USER_PASSWORD=secure_password_here
BASE_URL=https://staging.yourapp.com
```

### GitHub Secrets setup:
```yaml
# .github/workflows/playwright.yml
env:
  TEST_USER_EMAIL: ${{ secrets.TEST_USER_EMAIL }}
  TEST_USER_PASSWORD: ${{ secrets.TEST_USER_PASSWORD }}
  BASE_URL: ${{ secrets.BASE_URL }}
```

## Contents

- `docs/` — Step-by-step implementation guides
- `templates/` — Ready-to-copy project scaffold
- `playwright-ts-windsurf/` — Complete example implementation

## Quick Reference

- **Test naming**: `PT_LOGIN_loginWithValidCredentials_userIsLoggedIn`
- **Page Objects**: Extend `BasePage`, use `getByTestId()`
- **Fixtures**: Type-safe test dependencies in `src/fixtures/`
- **Data**: JSON files in `src/data/`, CSV support available
- **CI**: Parallel execution, trace/video on failure

## Integration Steps

1. **Read docs** in numeric order (`docs/00-overview.md` → `docs/08-debugging-and-reporting.md`)
2. **Copy templates** relevant to your project
3. **Configure credentials** using `.env` + GitHub Secrets
4. **Customize selectors** for your application
5. **Run tests** locally, then enable CI

This repository serves as the single source of truth for Playwright testing standards across all projects.
