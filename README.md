# **Playwright CI Reporter**

[![Build Status](https://github.com/deepakkamboj/playwright-test-reporter/actions/workflows/ci.yml/badge.svg)](https://github.com/deepakkamboj/playwright-test-reporter/actions/workflows/ci.yml)
[![npm version](https://img.shields.io/npm/v/playwright-test-reporter.svg)](https://www.npmjs.com/package/playwright-test-reporter)
[![TypeScript](https://img.shields.io/badge/TypeScript-4.9%2B-blue)](https://www.typescriptlang.org/)
[![Playwright](https://img.shields.io/badge/Playwright-1.50%2B-green)](https://playwright.dev/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

A modern, maintainable custom reporter for Playwright tests that enhances your test output with colorized results, comprehensive metrics, and intelligent failure analysis. Perfect for CI/CD pipelines and local development.

## **✨ Features**

- 🎨 **Smart Colorized Output**:

    - ✅ Passed tests (Green)
    - ❌ Failed tests (Red)
    - 🔄 Retry attempts (Yellow)
    - ⚠️ Skipped tests (Gray)
    - 🚀 Test run status (Bright Magenta)

- 📊 **Comprehensive Metrics**:

    - Total execution time with smart formatting
    - Average test duration analysis
    - Slowest test identification
    - Top slowest tests ranking
    - Pass/fail/skip statistics

- 🛠 **Advanced Features**:

    - Configurable slow test thresholds
    - Timeout warnings
    - Stack trace controls
    - Retry attempt tracking
    - CI integration with build information
    - Test history tracking and comparison
    - Team ownership assignment
    - Error categorization for failure analysis

- 📝 **Intelligent Reporting**:
    - Detailed failure analysis
    - Clear error messages
    - Formatted stack traces
    - Test timing insights
    - Skipped test warnings
    - Test history tracking
    - CI environment detection

## **🚀 Installation**

Install the package using npm:

```bash
npm install playwright-test-reporter --save-dev
```

---

## **Usage**

Integrate the `playwright-test-reporter` into your Playwright configuration file (`playwright.config.ts`):

```typescript
import {defineConfig} from '@playwright/test';

export default defineConfig({
    testDir: './tests', // Adjust to your test directory
    retries: 2, // Example of using retries
    reporter: [
        [
            'playwright-test-reporter',
            {
                slowTestThreshold: 3,
                maxSlowTestsToShow: 5,
                timeoutWarningThreshold: 20,
                showStackTrace: true,
                outputDir: './test-results',
            },
        ],
    ],
    use: {
        trace: 'on-first-retry', // Example: trace only on retries
        video: 'on-first-retry',
        screenshot: 'only-on-failure',
    },
});
```

### **Reporter Configuration Options**

| Option                    | Type      | Default          | Description                                              |
| ------------------------- | --------- | ---------------- | -------------------------------------------------------- |
| `slowTestThreshold`       | `number`  | `5`              | Time in seconds after which a test is considered slow    |
| `maxSlowTestsToShow`      | `number`  | `3`              | Maximum number of slowest tests to display in the report |
| `timeoutWarningThreshold` | `number`  | `30`             | Time in seconds after which to show a timeout warning    |
| `showStackTrace`          | `boolean` | `true`           | Whether to show stack traces in error reports            |
| `outputDir`               | `string`  | `./test-results` | Directory where JSON output files will be saved          |

### **Team Ownership**

You can specify test ownership by team using annotations:

```typescript
// Using annotations
test.describe('User authentication', () => {
    test.use({metadata: {team: 'Frontend'}});

    test('should login successfully', async ({page}) => {
        // Test implementation
    });
});

// Alternatively, by including team name in the test title
test('[Frontend] should login successfully', async ({page}) => {
    // Test implementation
});
```

### **Working with Test History**

The reporter includes utilities for working with test history:

```typescript
import {HistoryUtils} from 'playwright-test-reporter';

// Check if a test was failing in the previous run
const wasFailing = HistoryUtils.wasTestFailingPreviously('test-id-123');

// Compare current failures with previous run
const {newlyFailing, fixed} = HistoryUtils.compareWithPreviousRun(['test-id-456', 'test-id-789']);

console.log('New failures:', newlyFailing);
console.log('Fixed tests:', fixed);
```

### **JSON Output Files**

The reporter generates the following JSON files in the specified output directory:

- **testSummary.json**: Contains complete test run summary and metrics
- **testFailures.json**: Detailed information about test failures
- **.last-run.json**: Status of the last test run for comparison

These files can be used for:

- CI/CD pipeline integration
- Test history analysis
- Trend monitoring and reporting
- Build pass/fail decisions

---

## **📋 Output Examples**

### **Successful Run**

```plaintext
🚀 Starting test run: 3 tests using 2 workers
✅ Login test passed in 1.23s
✅ API integration test passed in 2.45s
⚠️ Payment test was skipped

✅ All 3 tests passed | 1 skipped | ⏱ Total: 3.68s

🖥️ Running locally

Additional Metrics:
- Average passed test time: 1.84s
- Slowest test took: 2.45s
- Top 3 slowest tests:
  1. API integration test: 2.45s
  2. Login test: 1.23s

⚠️ Warning: 1 test was skipped.
   Please ensure to test the skipped scenarios manually before deployment.
```

### **Failed Run**

```plaintext
🚀 Starting test run: 3 tests using 2 workers
✅ Login test passed in 1.23s
❌ API test failed in 2.45s
🔄 Retry attempt for "API test" (failed) in 2.50s
⚠️ Payment test was skipped

❌ 1 of 3 tests failed | 1 passed | 1 skipped | ⏱ Total: 6.18s

Additional Metrics:
- Average passed test time: 1.23s
- Slowest test took: 1.23s
- Top 3 slowest tests:
  1. Login test: 1.23s

Test Failures:
--- Failure #1 ---
  Test: API test
  Category: NetworkError
  Stack Trace:
    at Connection.connect (/src/api/connection.ts:45:7)
```

### **CI Run Output**

```plaintext
🚀 Starting test run: 8 tests using 2 workers
✅ Homepage test passed in 1.05s
✅ Product list test passed in 2.33s
❌ Checkout test failed in 3.12s

❌ 1 of 8 tests failed | 7 passed | 0 skipped | ⏱ Total: 12.48s

Build Information:
- CI System: GitHub Actions
- Build: 1234
- Branch: main
- Commit: abc12345
- Build link: https://github.com/user/repo/actions/runs/1234
- Artifacts: https://github.com/user/repo/actions/runs/1234/artifacts
```

## **🧰 Architecture**

The package consists of several core components:

1. **Reporter**: Main entry point that implements Playwright's Reporter interface
2. **TestUtils**: Utility functions for processing and calculating test metrics
3. **Logger**: Handles colorized console output formatting
4. **FileHandler**: Manages writing test results to JSON files
5. **HistoryUtils**: Provides functionality for test history comparison
6. **BuildInfoUtils**: Detects CI environment and extracts build information

## **🤝 Contributing**

We welcome contributions! Here's how you can help:

1. Fork the repository
2. Create your feature branch:
    ```bash
    git checkout -b feature/amazing-feature
    ```
3. Make your changes and commit them:
    ```bash
    git commit -m 'Add some amazing feature'
    ```
4. Push to your fork:
    ```bash
    git push origin feature/amazing-feature
    ```
5. Open a Pull Request

Please ensure your PR:

- Follows the existing code style
- Includes appropriate tests
- Updates documentation as needed
- Describes the changes made

---

## **📝 License**

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## **🙏 Acknowledgments**

- Built with [Playwright](https://playwright.dev/)
- Inspired by the need for better test reporting in CI/CD pipelines
- Thanks to all contributors who help make this reporter better
