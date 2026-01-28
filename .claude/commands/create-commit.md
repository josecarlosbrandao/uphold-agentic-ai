---
allowed-tools: Bash(git add:*), Bash(git branch:*), Bash(git checkout:*), Bash(git commit:*), Bash(git diff:*), Bash(git push:*), Bash(git status:*), Bash(git log:*), Skill(format-code:*), AskUserQuestion
description: Commit and push it to origin
---

## Context

- Current git status: !git status
- Current git diff (staged and unstaged changes): !git diff HEAD
- Current branch: !git branch --show-current

## Your task

Create a commit and push it to origin after formatting code and validating the branch.

**Rules:**
1. FORBID commits directly to main or master branches
2. Ask if user wants to commit to current branch or create a new branch
3. If creating a new branch, ask which branch should be used as the starting point (this will also be the PR target)
4. Format code before committing using the format-code command
5. Ask the user: do they want to provide a commit message, or should you generate one?
6. If user provides a message, use it exactly as provided
7. If user wants you to generate one, create an appropriate message based on the changes
8. Do NOT add "Co-Authored-By" or any other trailers to the commit message

**Steps:**

1. Check current branch:
   - Get current branch: `git branch --show-current`
   - If current branch is "main" or "master", STOP and inform the user:
     ```
     ❌ Cannot commit directly to main/master branch.
     Please create a feature branch first.
     ```

2. Use AskUserQuestion to ask the user about branching:
   - **Commit to current branch** (if not main/master)
   - **Create a new branch first**

   If user chooses to create a new branch:
   - Ask which branch should be used as the starting point (this will also be the target when creating a PR):
     - **main** (most common default)
     - **master** (legacy default)
     - **develop** (for gitflow workflows)
     - Let user specify "Other" for custom branch name
   - Ask for the new branch name
   - Create and checkout the new branch: `git checkout -b [new-branch-name] [starting-point-branch]`

3. Run format-code command:
   - Execute: `Skill format-code`
   - This will format and lint the code according to repository configuration
   - Stage any formatting changes: `git add -u` (only updates tracked files modified by formatting)

4. Ask the user: do they want to provide a commit message, or should you generate one?
   - If user provides a message, use it exactly as provided
   - If user wants you to generate one, create an appropriate message based on the changes

5. Stage and commit changes:
   - Stage changes: `git add .`
   - Commit with the message: `git commit -m "[message]"`

6. Push the commit to origin:
   - Push: `git push -u origin [current-branch]`
   - The `-u` flag sets up tracking for new branches

7. Get the commit hash:
   - `git log -1 --format=%H`

8. **Final feedback**: Output a summary:
   ```
   ## Commit Created

   - **Hash**: [short hash]
   - **Message**: [commit message first line]
   - **Branch**: [current branch]
   - **Pushed to**: origin/[current branch]
   - **Code formatted**: Yes
   ```
