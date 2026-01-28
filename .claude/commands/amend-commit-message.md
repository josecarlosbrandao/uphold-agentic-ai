---
allowed-tools: Bash(git commit --amend:*), Bash(git log:*), Bash(git push --force:*)
description: Amend the last commit message and force push
---

## Context

- Current branch: !`git branch --show-current`
- Last commit message: !`git log -1 --format=%B`

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
3. You have the capability to call multiple tools in a single response. You MUST do all of the above in a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.
