# Playwright-Zephyr Integration Instructions

## Overview

The `playwright-zephyr` library enables seamless integration between Playwright test automation and Zephyr test management system. This instruction file provides comprehensive guidance for implementing Zephyr reporting in Playwright + TypeScript projects following senior test automation engineer standards.

**Library Repository**: https://github.com/elaichenkov/playwright-zephyr

## What is Playwright-Zephyr?

Playwright-Zephyr is a custom reporter that automatically publishes Playwright test results to Zephyr (Jira Test Management). It supports both Zephyr Server and Zephyr Cloud, enabling:

- Automatic test execution reporting to Zephyr
- Test cycle creation and management
- Real-time test result synchronization
- Custom field support for enhanced reporting
- Integration with CI/CD pipelines

## Installation & Setup

### 1. Install the Library

```bash
npm install -D playwright-zephyr
```

### 2. Environment Configuration

Create environment variables for secure credential management:

```env
# .env file
ZEPHYR_AUTH_TOKEN=your_zephyr_authorization_token_here
ZEPHYR_PROJECT_KEY=YOUR_PROJECT_KEY
ZEPHYR_HOST=https://jira.your-company-domain.com/
```

**Security Note**: Never commit authorization tokens to version control. Always use environment variables or CI/CD secrets.

### 3. Obtain Zephyr Authorization Token

Follow the [official Zephyr documentation](https://tm4j-cloud.elevio.help/en/articles/164) to generate your authorization token.

## Configuration Patterns

### Basic Server Configuration

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [
    ['html'], // Keep HTML reporter for local development
    ['playwright-zephyr', {
      host: process.env.ZEPHYR_HOST,
      authorizationToken: process.env.ZEPHYR_AUTH_TOKEN,
      projectKey: process.env.ZEPHYR_PROJECT_KEY
    }]
  ],
  // ... other configuration
});
```

### Cloud Configuration with Custom Fields

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: [
    ['html'],
    ['playwright-zephyr/lib/src/cloud', {
      projectKey: process.env.ZEPHYR_PROJECT_KEY,
      authorizationToken: process.env.ZEPHYR_AUTH_TOKEN,
      testCycle: {
        name: `Automated Playwright Run - ${new Date().toISOString()}`,
        customFields: {
          Browser: 'Google Chrome',
          Device: 'MacOS',
          Environment: process.env.NODE_ENV || 'test',
          TestSuite: 'Regression',
          ExecutionType: 'Automated'
        }
      }
    }]
  ],
  // ... other configuration
});
```

### Advanced Configuration with Conditional Reporting

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

const isCI = !!process.env.CI;
const enableZephyrReporting = process.env.ENABLE_ZEPHYR_REPORTING === 'true';

const reporters: any[] = [['html']];

// Only add Zephyr reporter in CI or when explicitly enabled
if (isCI || enableZephyrReporting) {
  reporters.push(['playwright-zephyr/lib/src/cloud', {
    projectKey: process.env.ZEPHYR_PROJECT_KEY,
    authorizationToken: process.env.ZEPHYR_AUTH_TOKEN,
    testCycle: {
      name: `${process.env.CI ? 'CI' : 'Local'} - ${new Date().toISOString()}`,
      customFields: {
        Browser: 'Multi-Browser',
        Environment: process.env.NODE_ENV || 'test',
        BuildNumber: process.env.BUILD_NUMBER || 'local',
        GitBranch: process.env.GIT_BRANCH || 'unknown'
      }
    }
  }]);
}

export default defineConfig({
  reporter: reporters,
  // ... other configuration
});
```

## Test Case Implementation

### Test ID Annotation Pattern

Each test must include a unique Zephyr test case ID in square brackets:

```typescript
// ✅ Correct: Zephyr ID in square brackets
test('[PROJ-123] PT_LOGIN_loginWithValidCredentials_userIsLoggedIn', async ({ page }) => {
  // Test implementation
});

// ✅ Alternative: ID at the beginning
test('[PROJ-124] User can register with valid email', async ({ page }) => {
  // Test implementation
});

// ❌ Incorrect: Missing square brackets
test('PROJ-125 Login test', async ({ page }) => {
  // This won't be reported to Zephyr
});
```

### Enhanced Test Metadata

```typescript
import { test, expect } from '@playwright/test';

test.describe('Registration Tests', () => {
  test('[PROJ-101] PT_REGISTRATION_registerWithValidData_userIsRegisteredSuccessfully', async ({ page }) => {
    // Test Context: Critical user onboarding flow
    // Zephyr Test Case: PROJ-101
    // Priority: High
    
    await test.step('Navigate to registration page', async () => {
      await page.goto('/register');
    });

    await test.step('Fill registration form', async () => {
      await page.fill('[data-test="email"]', 'test@example.com');
      await page.fill('[data-test="password"]', 'SecurePass123!');
    });

    await test.step('Submit registration', async () => {
      await page.click('[data-test="register-button"]');
    });

    await test.step('Verify successful registration', async () => {
      await expect(page.locator('[data-test="success-message"]')).toBeVisible();
    });
  });
});
```

### Data-Driven Tests with Zephyr Integration

```typescript
const testCases = [
  { id: 'PROJ-201', email: 'valid@example.com', password: 'Valid123!', expected: 'success' },
  { id: 'PROJ-202', email: 'invalid-email', password: 'Valid123!', expected: 'error' },
  { id: 'PROJ-203', email: 'valid@example.com', password: '123', expected: 'error' }
];

testCases.forEach(({ id, email, password, expected }) => {
  test(`[${id}] Registration with ${email}`, async ({ page }) => {
    await page.goto('/register');
    await page.fill('[data-test="email"]', email);
    await page.fill('[data-test="password"]', password);
    await page.click('[data-test="register-button"]');
    
    if (expected === 'success') {
      await expect(page.locator('[data-test="success-message"]')).toBeVisible();
    } else {
      await expect(page.locator('[data-test="error-message"]')).toBeVisible();
    }
  });
});
```

## Best Practices

### 1. Test Case Management

```typescript
// Create a utility for managing Zephyr test IDs
export class ZephyrTestManager {
  static formatTestName(zephyrId: string, testName: string): string {
    return `[${zephyrId}] ${testName}`;
  }
  
  static validateTestId(testName: string): boolean {
    return /\[[\w-]+\]/.test(testName);
  }
}

// Usage
test(ZephyrTestManager.formatTestName('PROJ-123', 'PT_LOGIN_validCredentials_success'), 
  async ({ page }) => {
    // Test implementation
  }
);
```

### 2. Environment-Specific Configuration

```typescript
// config/zephyr.config.ts
export const getZephyrConfig = () => {
  const environment = process.env.NODE_ENV || 'test';
  
  return {
    projectKey: process.env.ZEPHYR_PROJECT_KEY,
    authorizationToken: process.env.ZEPHYR_AUTH_TOKEN,
    testCycle: {
      name: `Automated Tests - ${environment.toUpperCase()} - ${new Date().toISOString()}`,
      customFields: {
        Environment: environment,
        Browser: 'Multi-Browser',
        TestType: 'Automated',
        Framework: 'Playwright',
        Language: 'TypeScript'
      }
    }
  };
};
```

### 3. Error Handling and Fallbacks

```typescript
// playwright.config.ts
const createZephyrReporter = () => {
  try {
    if (!process.env.ZEPHYR_AUTH_TOKEN) {
      console.warn('⚠️  Zephyr auth token not found. Skipping Zephyr reporting.');
      return null;
    }
    
    return ['playwright-zephyr/lib/src/cloud', getZephyrConfig()];
  } catch (error) {
    console.error('❌ Failed to configure Zephyr reporter:', error);
    return null;
  }
};

const reporters = [['html']];
const zephyrReporter = createZephyrReporter();
if (zephyrReporter) {
  reporters.push(zephyrReporter);
}

export default defineConfig({
  reporter: reporters,
  // ... other configuration
});
```

## CI/CD Integration

### GitHub Actions Example

```yaml
# .github/workflows/playwright-zephyr.yml
name: Playwright Tests with Zephyr Reporting

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: '18'
          
      - name: Install dependencies
        run: npm ci
        
      - name: Install Playwright browsers
        run: npx playwright install --with-deps
        
      - name: Run Playwright tests with Zephyr reporting
        run: npx playwright test
        env:
          ZEPHYR_AUTH_TOKEN: ${{ secrets.ZEPHYR_AUTH_TOKEN }}
          ZEPHYR_PROJECT_KEY: ${{ secrets.ZEPHYR_PROJECT_KEY }}
          ZEPHYR_HOST: ${{ secrets.ZEPHYR_HOST }}
          ENABLE_ZEPHYR_REPORTING: 'true'
          NODE_ENV: 'ci'
          BUILD_NUMBER: ${{ github.run_number }}
          GIT_BRANCH: ${{ github.ref_name }}
```

### Jenkins Pipeline Example

```groovy
pipeline {
    agent any
    
    environment {
        ZEPHYR_AUTH_TOKEN = credentials('zephyr-auth-token')
        ZEPHYR_PROJECT_KEY = credentials('zephyr-project-key')
        ZEPHYR_HOST = credentials('zephyr-host')
        ENABLE_ZEPHYR_REPORTING = 'true'
    }
    
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
                sh 'npx playwright install --with-deps'
            }
        }
        
        stage('Run Tests') {
            steps {
                sh 'npx playwright test'
            }
            post {
                always {
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'playwright-report',
                        reportFiles: 'index.html',
                        reportName: 'Playwright Report'
                    ])
                }
            }
        }
    }
}
```

## Troubleshooting

### Common Issues and Solutions

#### 1. Authentication Errors
```bash
# Error: Unauthorized (401)
# Solution: Verify your authorization token
echo $ZEPHYR_AUTH_TOKEN  # Should not be empty
```

#### 2. Test Cases Not Appearing in Zephyr
```typescript
// Issue: Missing square brackets in test name
test('Login test', async ({ page }) => {  // ❌ Won't sync
  // ...
});

// Solution: Add Zephyr ID in square brackets
test('[PROJ-123] Login test', async ({ page }) => {  // ✅ Will sync
  // ...
});
```

#### 3. Network Connectivity Issues
```typescript
// Add timeout and retry configuration
export default defineConfig({
  reporter: [
    ['playwright-zephyr/lib/src/cloud', {
      // ... other config
      timeout: 30000,  // 30 second timeout
      retries: 3       // Retry failed requests
    }]
  ]
});
```

### Debug Mode

Enable debug logging to troubleshoot issues:

```bash
# Enable debug mode
DEBUG=playwright-zephyr* npx playwright test

# Or set environment variable
export DEBUG=playwright-zephyr*
npx playwright test
```

## Security Considerations

### 1. Token Management
- Store tokens in environment variables or CI/CD secrets
- Use different tokens for different environments
- Regularly rotate authorization tokens
- Never commit tokens to version control

### 2. Access Control
```typescript
// Restrict Zephyr reporting to authorized environments
const allowedEnvironments = ['staging', 'production', 'ci'];
const currentEnv = process.env.NODE_ENV;

if (allowedEnvironments.includes(currentEnv)) {
  // Enable Zephyr reporting
} else {
  console.log('Zephyr reporting disabled for environment:', currentEnv);
}
```

## Monitoring and Maintenance

### 1. Test Execution Monitoring
```typescript
// Add custom logging for Zephyr integration
test.afterEach(async ({ }, testInfo) => {
  if (testInfo.status === 'failed') {
    console.log(`❌ Test ${testInfo.title} failed - will be reported to Zephyr`);
  } else {
    console.log(`✅ Test ${testInfo.title} passed - reported to Zephyr`);
  }
});
```

### 2. Regular Maintenance Tasks
- Review and update test case mappings monthly
- Validate Zephyr connectivity in CI/CD pipelines
- Monitor test execution trends in Zephyr dashboards
- Update authorization tokens before expiration

## Integration with Existing Projects

### 1. Gradual Migration
```typescript
// Phase 1: Add Zephyr IDs to existing tests
test('[EXISTING-001] existing test name', async ({ page }) => {
  // Existing test implementation
});

// Phase 2: Enable Zephyr reporting for specific test suites
test.describe('Critical Path Tests', () => {
  // Only these tests will be reported to Zephyr initially
});
```

### 2. Backward Compatibility
```typescript
// Support both old and new test naming conventions
const hasZephyrId = (testName: string) => /\[[\w-]+\]/.test(testName);

test.beforeEach(async ({ }, testInfo) => {
  if (!hasZephyrId(testInfo.title)) {
    console.warn(`⚠️  Test "${testInfo.title}" missing Zephyr ID - won't be reported`);
  }
});
```

---

**Note**: This integration enhances test reporting capabilities while maintaining the existing Playwright test structure. Always test the integration in a non-production environment before deploying to production systems.
