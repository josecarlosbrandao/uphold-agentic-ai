---
allowed-tools: Bash(npm:*), Bash(yarn:*), Bash(pnpm:*), Bash(bun:*), Bash(eslint:*), Bash(prettier:*), Bash(go fmt:*), Read, AskUserQuestion
description: Format and lint code using repository configuration
---

## Context

- Current working directory: !pwd

## Your task

Format and lint the code in the repository using the configured linting/formatting tools.

**Rules:**
1. Check if pre-commit hooks handle formatting automatically (if so, skip manual formatting for speed)
2. Discover which linting/formatting tools are configured in the repository
3. Run the appropriate commands with --fix or equivalent flags to automatically fix issues
4. Report any issues that couldn't be automatically fixed

**Steps:**

0. Check for pre-commit hooks that handle formatting:
   - If pre-commit hooks are configured with formatting tools:
     ```
     ## Pre-commit Hooks Detected

     This repository has pre-commit hooks configured that will automatically format code on commit.
     Skipping manual formatting for better performance.

     Note: Formatting will run automatically when you commit.
     ```
     Then STOP and skip the remaining steps.

1. Identify the linting/formatting tools configured in the repository:
   - Check package.json for scripts like: `"lint"`, `"lint:fix"`, `"format"`, `"prettier"`, etc.
   - Check for configuration files:
     - ESLint: `.eslintrc.js`, `.eslintrc.json`, `eslint.config.js`, or `eslint` field in package.json
     - Prettier: `.prettierrc`, `.prettierrc.json`, `prettier.config.js`, or `prettier` field in package.json
     - Go: Go projects typically use `go fmt`
     - Other language-specific formatters

2. Determine the appropriate commands:
   - **If package.json has lint/format scripts**: Use those (they're project-specific)
     - Examples: `npm run lint:fix`, `npm run format`, `yarn lint --fix`
   - **If using ESLint**: `npx eslint . --fix` or `npm run lint -- --fix`
   - **If using Prettier**: `npx prettier --write .` or `npm run format`
   - **If using both ESLint and Prettier**: Run both commands
   - **Go**: `go fmt ./...`

3. Execute the formatting/linting commands:
   - Run each command identified in step 2
   - Capture the output to see what was changed
   - If any command fails or reports unfixable issues, note them

4. **Final feedback**: Output a summary:
   ```
   ## Code Formatted

   - **Tools used**: [list of tools: ESLint, Prettier, etc.]
   - **Commands executed**: [list of commands run]
   - **Files modified**: [number of files modified or "see command output"]
   - **Remaining issues**: [any issues that couldn't be auto-fixed, or "None"]
   ```

**Notes:**
- If pre-commit hooks are detected in step 0, skip manual formatting since it will run automatically on commit
- If no linting/formatting tools are configured, inform the user
- Prefer using package.json scripts when available as they contain project-specific configuration
