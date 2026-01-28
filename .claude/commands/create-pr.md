---
allowed-tools: Bash(gh pr create:*), Bash(git branch:*), Bash(git log:*), Bash(git merge-base:*), Bash(tail:*), Bash(test:*), Read, AskUserQuestion
description: Create a pull request for current branch
---

## Context

- Current branch: !git branch --show-current

## Your task

Create a pull request using the first commit message in this branch.

**Steps:**

1. Use AskUserQuestion to ask the user which branch should be the PR target (where it will be merged to). Provide these options:
   - **main** (most common default)
   - **master** (legacy default)
   - **develop** (for gitflow workflows)
   - Let user specify "Other" for custom branch name

2. Find the merge-base between HEAD and the target branch selected in step 1:
   ```bash
   git merge-base HEAD <target-branch>
   ```

3. Get the first commit subject using the merge-base from step 2:
   ```bash
   git log --format=%s <merge-base>..HEAD | tail -1
   ```

4. Get the first commit body using the merge-base from step 2:
   ```bash
   git log --format=%b <merge-base>..HEAD | tail -1
   ```

5. Use AskUserQuestion to ask if the user wants to link Jira tickets to this PR:
   - **No** - Skip linking tickets
   - Provide an input option where user can write all Jira ticket IDs immediately (e.g., "PROJ-123" or "PROJ-123, PROJ-456" for multiple tickets)

6. Check for PR template and build the body:
   - Check if `.github/PULL_REQUEST_TEMPLATE.md` exists: `test -f .github/PULL_REQUEST_TEMPLATE.md && echo "exists"`
   - If template exists:
     - Read the template using the Read tool
     - Fill in the `## Description` section with the commit body from step 4
     - Fill in the `## Related Issues` section with Jira tickets from step 5 (if any) as bullet points:
       ```
       - [TICKET-123](https://uphold.atlassian.net/browse/TICKET-123)
       - [TICKET-456](https://uphold.atlassian.net/browse/TICKET-456)
       ```
     - Keep all other sections from the template intact (use "N/A" for sections that don't apply)
   - If no template exists:
     - Use the commit body from step 4 as the PR body
     - If Jira tickets were provided, append them at the end under a "## Related Issues" heading

7. Use AskUserQuestion to ask the user two things:

   a) **PR Status**: Should the PR be created as draft or ready for review?
      - **Ready for review** (default)
      - **Draft** - Create as draft PR

   b) **Reviewers**: Do you want to add reviewers to this PR?
      - **No** - Skip adding reviewers
      - Provide an input option where user can write all GitHub usernames (e.g., "username1" or "username1, username2, username3")

8. Create the PR using `gh pr create`:
   - Use `--base <target-branch>` to specify the target branch from step 1
   - Use `--title` set to the first commit subject from step 3
   - Use `--body` set to the filled template or commit body from step 6
   - If user chose "Draft" in step 7a, add `--draft` flag
   - If user provided reviewers in step 7b:
     - Add `--reviewer username1,username2,username3` to the gh command
     - If gh fails to add a reviewer (e.g., user not found), alert the user with a warning message specifying which reviewer(s) could not be added
     - Continue adding remaining valid reviewers if any
     - Note: If the gh CLI doesn't support adding reviewers or fails, skip this part and inform the user they need to add reviewers manually via GitHub UI

9. **Final feedback**: After the PR is created, output a summary:
   ```
   ## PR Created

   - **URL**: [PR URL from gh output]
   - **Title**: [PR title]
   - **Status**: [Draft or Ready for review]
   - **Target branch**: [target branch] ← [current branch]
   - **Linked tickets**: [list of tickets or "None"]
   - **Reviewers**: [list of added reviewers or "None"]
   ```

   If any reviewers failed to be added, include a warning:
   ```
   ⚠️ Could not add reviewer(s): [list of failed usernames]
   ```
