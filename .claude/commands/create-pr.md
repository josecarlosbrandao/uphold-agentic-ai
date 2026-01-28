---
allowed-tools: Bash(gh pr create:*)
description: Create a pull request for current branch
---

## Context

- Current branch: !`git branch --show-current`
- First commit subject: !`git log --format=%s $(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null)..HEAD | tail -1`
- First commit body: !`git log --format=%b $(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null)..HEAD | tail -1`

## Your task

Create a pull request using the first commit message.

1. Use `gh pr create` with `--title` set to the first commit subject
2. Use `--body` set to the first commit body (or empty if none)
3. You have the capability to call multiple tools in a single response. You MUST do all of the above in a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.
