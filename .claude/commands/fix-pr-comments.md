---
allowed-tools: Bash(gh pr checkout:*), Bash(gh pr view:*), Bash(gh api:*), Bash(gh repo view:*), Bash(git diff:*), Bash(git log:*), Bash(git status:*), Bash(git branch:*), Bash(git stash:*), Bash(git checkout:*), Bash(git clean:*), Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(git remote:*), Bash(npm test:*), Bash(npm run test:*), Bash(yarn test:*), Bash(yarn run test:*), Bash(pnpm test:*), Task, Read, Glob, Grep, Edit, Write, AskUserQuestion, TodoWrite
description: Get PR review comments and fix them
---

## Your task

Fetch all review comments from a pull request and help fix the issues raised.

### Step 1: Get PR identifier

Use AskUserQuestion to ask the user for the PR number or URL they want to fix comments for.

### Step 2: Verify PR belongs to current repository

Check if we're in a git repository and if the PR belongs to it:
```
gh repo view --json nameWithOwner -q '.nameWithOwner'
gh pr view {pr_number} --json headRepository -q '.headRepository.nameWithOwner'
```

Compare the results. If the PR doesn't belong to the current repo:
- Inform the user that the PR is from a different repository
- Stop the command and do not proceed further

### Step 3: Check for uncommitted changes

Before switching branches, check current state:
```
git status --porcelain
git branch --show-current
```

If there are uncommitted changes (output is not empty), use AskUserQuestion to ask the user:
- **Stash changes**: Save current work and proceed
- **Discard changes**: Lose current work and proceed
- **Stop**: Cancel the command, do nothing else

If user chooses to stash:
```
git stash push -m "Auto-stash before fixing PR comments"
```

If user chooses to discard:
```
git checkout -- .
git clean -fd
```

If user chooses to stop: End the command immediately without any further actions.

### Step 4: Checkout the PR branch

```
gh pr checkout {pr_number}
```

### Step 5: Gather PR comments

Get all review comments from the PR (include the comment ID for later resolution):
```
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments --jq '.[] | {id: .id, node_id: .node_id, path: .path, line: .line, body: .body, user: .user.login}'
```

Also get the general PR review comments:
```
gh pr view {pr_number} --comments
```

**Important:** Keep track of comment IDs for comments that get fixed - you'll need them in Step 14 to mark them as resolved.

### Step 6: Create a task list

Use the TodoWrite tool to create a todo item for each actionable comment that needs to be addressed. Skip comments that are:
- Already resolved
- Just acknowledgments or praise
- Questions that don't require code changes

### Step 7: Present the comments

Present all comments to the user in this format:

```
## PR Comments to Address

### Comment 1
- **File**: [path/to/file.ts](path/to/file.ts)
- **Line**: 42
- **Author**: username
- **Comment**: [the actual comment]

[Repeat for each comment]
```

### Step 8: Ask for confirmation

Use AskUserQuestion to ask the user which comments they want to fix:
- All comments
- Select specific comments
- Skip this step and see the plan first

### Step 9: Analyze and fix each comment

For each comment to fix:
1. Read the relevant file and understand the context
2. Analyze what change is needed based on the comment
3. **If the comment is ambiguous or there are multiple valid approaches**, use AskUserQuestion to:
   - Clarify what the reviewer meant
   - Present different implementation options and let the user choose
   - Confirm assumptions before making changes
4. Make the fix using the Edit tool
5. Mark the todo item as completed

**Important:** Do NOT guess when uncertain. Always ask the user for clarification or approach selection when:
- The comment could be interpreted in multiple ways
- There are multiple valid solutions to the problem
- The fix might have side effects or impact other parts of the code
- You're unsure if the reviewer wants a quick fix or a more thorough refactor

### Step 10: Run tests

Ask the user what test command to run (e.g., `npm test`, `yarn test`, `pnpm test`). Then run the tests.

If tests fail:
1. Analyze the failure output
2. Identify which fix caused the failure
3. Go back to Step 9 to fix the failing test
4. Re-run tests until they pass

Repeat this cycle until all tests pass. If stuck, use AskUserQuestion to get guidance from the user.

### Step 11: Review changes

After all fixes are made and tests pass:
```
git diff
git status
```

Present a summary of all changes made.

### Step 12: Ask about committing

Use AskUserQuestion to ask if the user wants to:
- Commit and push the changes
- Review changes first
- Make additional modifications

### Step 13: Commit and push (if approved)

If the user confirms:
```
git add -A
git commit -m "Address PR review comments"
git push
```

**Important:** Do NOT add "Co-Authored-By" or any other trailers to the commit message.

### Step 14: Resolve fixed comments on GitHub

After pushing, resolve only the comments that were actually fixed using the GitHub API:
```
gh api graphql -f query='mutation { minimizeComment(input: {subjectId: "{comment_node_id}", classifier: RESOLVED}) { minimizedComment { isMinimized } } }'
```

Or use the REST API to mark review comments as resolved:
```
gh api repos/{owner}/{repo}/pulls/{pr_number}/comments/{comment_id}/replies -f body="Resolved in latest push"
```

**Important:**
- Only resolve comments that were explicitly fixed
- Do NOT resolve comments that were:
  - Skipped by user choice
  - Left ambiguous and not addressed
  - Questions that weren't answered
  - Comments the user chose not to fix

**Guidelines:**
- Be precise with fixes - only change what the comment asks for
- **Always ask questions when uncertain** - never guess or assume
- If a comment is ambiguous, ask for clarification before making changes
- When multiple solutions exist, present options and let the user decide
- If a fix seems risky or could break other things, confirm with the user first
- Preserve existing code style and formatting
- Tests must pass before committing - iterate until green
- Only resolve comments that were actually fixed, leave others untouched
