---
allowed-tools: Bash(git rebase:*), Bash(git log:*), Bash(git rev-list:*), Bash(git merge-base:*), Bash(git branch:*), Bash(git commit:*), Bash(sed:*), Bash(tail:*), Bash(head:*)
description: Squash all commits in current branch into the first one
---

## Context

- Current branch: !git branch --show-current

## Your task

Squash all commits in the current branch into the first commit, keeping only the first commit message.

**Pre-checks:**

First, determine the merge-base and commit count:
1. Find merge-base: `git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null`
2. Count commits: `git rev-list --count HEAD ^<merge-base>`
3. Get first commit message: `git log --format=%B <merge-base>..HEAD | tail -n +1 | head -n 1`

**Rules:**
1. If current branch is `main` or `master`, tell the user this command cannot run on main/master and stop
2. If there is only 1 commit (or 0), tell the user there's nothing to squash and stop
3. If there are 2+ commits, use `git rebase -i` with `GIT_SEQUENCE_EDITOR` to automatically pick the first commit and squash the rest
4. The first commit message should be preserved; all other messages are discarded

**Squash command pattern:**
```bash
GIT_SEQUENCE_EDITOR="sed -i '' '2,\$s/^pick/squash/'" git rebase -i $(git merge-base HEAD main 2>/dev/null || git merge-base HEAD master 2>/dev/null)
```

Then fixup the commit message to keep only the first one:
```bash
GIT_SEQUENCE_EDITOR="sed -i '' '2,\$d'" git commit --amend
```

**Final feedback**: After squashing, output a summary:
```
## Commits Squashed

- **Branch**: [current branch]
- **Commits squashed**: [original count] → 1
- **Final commit hash**: [new short hash]
- **Final commit message**: [preserved message first line]
```
