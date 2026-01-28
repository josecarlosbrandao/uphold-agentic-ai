---
allowed-tools: mcp__atlassian__atlassianUserInfo, mcp__atlassian__getAccessibleAtlassianResources, mcp__atlassian__getJiraIssue, mcp__atlassian__getTransitionsForJiraIssue, mcp__atlassian__transitionJiraIssue, AskUserQuestion
description: Update status of Jira tasks assigned to the current user
---

## Your task

Update the status of one or more Jira tasks assigned to the current user. This command validates that tasks are assigned to you before making any changes.

**Steps:**

1. Get the current user's information:
   - Use `mcp__atlassian__atlassianUserInfo` to get the current user's account ID and display name

2. Get the Atlassian cloud ID:
   - Use `mcp__atlassian__getAccessibleAtlassianResources` to get the cloud ID

3. Use AskUserQuestion to ask for the ticket ID(s) to update:
   - Ask: "Which ticket(s) do you want to update?"
   - Accept single ticket (e.g., "PROJ-123") or multiple tickets separated by commas (e.g., "PROJ-123, PROJ-456, PROJ-789")

4. For EACH ticket provided, validate assignment:
   - Use `mcp__atlassian__getJiraIssue` to get the ticket details
   - Extract the assignee account ID from the response (fields.assignee.accountId)
   - Compare with current user's account ID from step 1
   - If ticket is NOT assigned to current user:
     - Add to a list of unauthorized tickets
     - Do NOT proceed with status change for this ticket
   - If ticket IS assigned to current user:
     - Add to a list of authorized tickets
     - Continue with the next step

5. If there are unauthorized tickets:
   - Display a warning and STOP:
   ```
   ⚠️  Cannot Update Tickets

   The following tickets are NOT assigned to you:
   - PROJ-123 (assigned to: [other user name or "Unassigned"])
   - PROJ-456 (assigned to: [other user name or "Unassigned"])

   You can only update tickets that are assigned to you.

   Authorized tickets (not changed):
   - PROJ-789
   ```
   - Do NOT make any changes to ANY tickets (even the authorized ones)

6. If ALL tickets are authorized, proceed with status update:
   - For each authorized ticket:
     - Use `mcp__atlassian__getTransitionsForJiraIssue` to get available status transitions
     - Display current status and available transitions

7. Use AskUserQuestion to ask which status to transition to:
   - Show the list of available transitions from step 6
   - Let user select the target status
   - Note: If multiple tickets are being updated and they have different available transitions, ask for each ticket separately

8. Execute the status transition:
   - For each ticket:
     - Use `mcp__atlassian__transitionJiraIssue` with the selected transition ID
     - Capture the result

9. **Final feedback**: Display a summary:
   ```
   ## Jira Tickets Updated

   **User**: [display name] ([account ID])
   **Tickets updated**: [count]

   | Key | Previous Status | New Status | Result |
   |-----|----------------|------------|--------|
   | PROJ-123 | To Do | In Progress | ✅ Updated |
   | PROJ-456 | In Progress | Done | ✅ Updated |
   ```

   If any updates failed:
   ```
   ## Jira Tickets Update Results

   **Successful**: [count]
   - PROJ-123: To Do → In Progress
   - PROJ-456: In Progress → Done

   **Failed**: [count]
   - PROJ-789: Error: [error message]
   ```

**Important Rules:**
- ALWAYS validate that tickets are assigned to the current user before making ANY changes
- If ANY ticket is not assigned to the user, STOP and do NOT update ANY tickets (even the authorized ones)
- Show clear warnings when tickets are not assigned to the user
- Never proceed with updates if validation fails
