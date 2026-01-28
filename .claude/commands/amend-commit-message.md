---
allowed-tools: Bash(git branch:*), Bash(git commit --amend:*), Bash(git log:*), Bash(git push --force:*), AskUserQuestion
description: Amend the last commit message and force push
---

## Context

- Current branch: !git branch --show-current
- Last commit message: !git log -1 --format=%B

## Your task

Amend the last commit message.

**Rules:**
1. If current branch is `main` or `master`, tell the user this command cannot run on main/master and stop
2. Ask the user: do they want to provide a new commit message, or should you generate a better one?
3. If user provides a message, use it exactly as provided
4. If user wants you to generate one, create an appropriate message based on the commit changes
5. Do NOT add "Co-Authored-By" or any other trailers to the commit message

**Steps:**
1. Use `git commit --amend -m "new message"` to change the last commit message
2. Use `git push --force` to push the amended commit
3. Get the new commit hash: `git log -1 --format=%H`
4. **Final feedback**: Output a summary:
   ```
   ## Commit Amended

   - **Hash**: [new short hash]
   - **Old message**: [previous message first line]
   - **New message**: [new message first line]
   - **Branch**: [current branch]
   - **Force pushed to**: origin/[current branch]
   ```
