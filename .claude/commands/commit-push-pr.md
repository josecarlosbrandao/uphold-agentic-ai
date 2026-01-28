---
allowed-tools: Bash(git add:*), Bash(git commit:*), Bash(git push:*), Bash(gh pr create:*)
description: Commit, push, and open a PR
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`

## Your task

This command chains `create-commit` and `create-pr`:

1. Stage and commit changes with an appropriate message
2. Push the commit to origin
3. Create a pull request using `gh pr create`
4. You have the capability to call multiple tools in a single response. You MUST do all of the above in a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.

**Important:** Do NOT add "Co-Authored-By" or any other trailers to the commit message.
