---
allowed-tools: Bash(git add:*), Bash(git push:*), Bash(git commit:*)
description: Commit and push it to origin
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`

## Your task

Create a commit and push it to origin.

**Rules:**
1. Ask the user: do they want to provide a commit message, or should you generate one?
2. If user provides a message, use it exactly as provided
3. If user wants you to generate one, create an appropriate message based on the changes
4. Do NOT add "Co-Authored-By" or any other trailers to the commit message

**Steps:**
1. Stage and commit changes with the message
2. Push the commit to origin
3. You have the capability to call multiple tools in a single response. You MUST do all of the above in a single message. Do not use any other tools or do anything else. Do not send any other text or messages besides these tool calls.
