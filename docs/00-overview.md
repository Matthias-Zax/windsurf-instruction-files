# 00 — Overview

This guide standardizes how to build Playwright + TypeScript tests that are maintainable, scalable, and CI-ready.

## Goals

- Clean architecture driven by Page Objects and typed fixtures
- Deterministic, parallel-friendly tests
- Data-driven scenarios with JSON (and optional CSV)
- First-class debugging and actionable reports
- Seamless CI integration

## Prerequisites

- Node.js 18+ (20+ recommended)
- npm 8+
- Basic familiarity with Playwright and TypeScript

## Quickstart

- Copy `templates/` into your repo root.
- Run:

```bash
npm ci
npx playwright install
npm test
```

Open `playwright-report/index.html` for the HTML report.
