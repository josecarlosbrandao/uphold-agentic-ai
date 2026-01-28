---
allowed-tools: Bash(git diff:*), Bash(git show:*), Bash(npm:*), Bash(yarn:*), Bash(pnpm:*), Bash(bun:*), Bash(jest:*), Bash(vitest:*), Bash(mocha:*), Read, Glob, Grep, Edit, Write, AskUserQuestion
description: Create tests for staged changes and last commit
---

## Context

- Current git status: !git status
- Staged changes: !git diff --cached --name-only
- Last commit: !git log -1 --oneline

## Your task

Create tests for the current staged changes. If there are no staged changes, create tests for the last commit in the current branch.

**Rules:**
1. **CRITICAL**: ALWAYS detect the package manager and verify test commands exist BEFORE running anything
   - Never assume npm/yarn/pnpm/bun
   - Never assume test commands exist
   - Check lock files and package.json first
2. Identify which files need tests based on staged changes or last commit
3. Analyze existing test files to understand the testing framework, patterns, and conventions
4. Follow the same test structure, naming conventions, and syntax used in the repository
5. Create or update test files following the repository standards
6. Run the tests using the verified command to confirm they work correctly

**Steps:**

1. Identify files that need testing:
   - If there are staged changes: `git diff --cached --name-only`
   - If no staged changes: `git show --name-only --format="" HEAD`
   - Filter out test files, config files, and non-code files

2. Discover the testing framework and conventions:
   - Search for existing test files using Glob (e.g., `**/*.test.js`, `**/*.spec.ts`)
   - Read a few test files to understand:
     - Testing framework (Jest, Vitest, Mocha, etc.)
     - File naming conventions (*.test.js, *.spec.ts, etc.)
     - Test structure and patterns
     - Import/require syntax
     - Assertion library
     - Mock/stub patterns
     - File organization (same directory, separate test directory, etc.)

3. **CRITICAL: Detect package manager and verify test command exists**:

   **Step 3a - Detect the package manager (in priority order):**
   - Check for `bun.lockb` → use `bun`
   - Check for `pnpm-lock.yaml` → use `pnpm`
   - Check for `yarn.lock` → use `yarn`
   - Check for `package-lock.json` → use `npm`
   - If no lock file, check package.json "packageManager" field
   - Default to `npm` only if nothing else is found

   **Step 3b - Read package.json and verify test scripts exist:**
   - Read package.json file
   - Check the "scripts" section for available test commands
   - Common test script names: `"test"`, `"test:unit"`, `"test:watch"`, etc.
   - **NEVER assume a command exists** - only use commands that are actually defined

   **Step 3c - Build the correct test command:**
   - Combine the detected package manager with the verified script name
   - Examples:
     - If yarn + "test" script exists → `yarn test`
     - If pnpm + "test:unit" script exists → `pnpm test:unit`
     - If bun + "test" script exists → `bun test`
   - If no test script exists in package.json, check for:
     - Test configuration files: jest.config.js, vitest.config.ts, etc.
     - Makefile with test targets
     - Direct test runner commands if globally installed

4. For each file identified in step 1:
   - Read the source file to understand what it does
   - Determine the test file path following the repository conventions
   - Create or update the test file with comprehensive tests:
     - Test main functionality
     - Test edge cases
     - Test error handling (if applicable)
     - Follow the same structure as existing tests
     - Use the same assertion syntax and patterns
     - Include appropriate test descriptions

5. **Run the test command to verify the tests work:**
   - **CRITICAL**: Use ONLY the exact command identified and verified in step 3
   - **NEVER run commands like `npm test` if the project uses yarn/pnpm/bun**
   - **NEVER assume commands exist** - if no test command was found in step 3, ask the user how to run tests
   - If tests fail, analyze the output and fix them
   - Ensure all new tests pass before completing the task
   - If the test command fails because it doesn't exist, re-check step 3 and verify your detection logic

6. **Final feedback**: Output a summary:
   ```
   ## Tests Created

   - **Files tested**: [list of source files]
   - **Test files**: [list of test files created/updated]
   - **Framework**: [testing framework used]
   - **Package manager**: [detected package manager: npm/yarn/pnpm/bun]
   - **Test command**: [exact command used to run tests]
   - **Test results**: [all tests passing / X tests failing]
   ```
