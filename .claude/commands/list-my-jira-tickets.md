---
allowed-tools: mcp__atlassian__atlassianUserInfo, mcp__atlassian__getAccessibleAtlassianResources, mcp__atlassian__searchJiraIssuesUsingJql, AskUserQuestion
description: List Jira tasks assigned to the current user
---

## Your task

List Jira tasks that are assigned to the current user, with optional filtering.

**Steps:**

1. Get the current user's information:
   - Use `mcp__atlassian__atlassianUserInfo` to get the current user's account ID and display name

2. Get the Atlassian cloud ID:
   - Use `mcp__atlassian__getAccessibleAtlassianResources` to get the cloud ID

3. Use AskUserQuestion to ask the user how they want to filter the tickets:
   - **All my tickets** - Show all tickets assigned to me
   - **By status** - Filter by specific status (To Do, In Progress, Done, etc.)
   - **By project** - Filter by specific project key (e.g., PROJ, DEV)
   - **Recent** - Show only tickets updated in the last 7 days

   If user selects "By status", ask them to specify the status name
   If user selects "By project", ask them to specify the project key

4. Build and execute the JQL query:
   - Base query: `assignee = currentUser()`
   - Add filters based on user selection:
     - All: `assignee = currentUser() ORDER BY updated DESC`
     - By status: `assignee = currentUser() AND status = "[status]" ORDER BY updated DESC`
     - By project: `assignee = currentUser() AND project = "[project]" ORDER BY updated DESC`
     - Recent: `assignee = currentUser() AND updated >= -7d ORDER BY updated DESC`
   - Use `mcp__atlassian__searchJiraIssuesUsingJql` with the JQL query
   - Request fields: `["summary", "status", "priority", "issuetype", "updated", "project"]`
   - Set maxResults to 50

5. **Final feedback**: Display the results in a formatted table:
   ```
   ## My Jira Tickets

   **User**: [display name] ([account ID])
   **Filter**: [filter description]
   **Total tickets**: [count]

   | Key | Summary | Status | Priority | Type | Project | Updated |
   |-----|---------|--------|----------|------|---------|---------|
   | PROJ-123 | Implement feature X | In Progress | High | Story | PROJ | 2 days ago |
   | PROJ-124 | Fix bug Y | To Do | Medium | Bug | PROJ | 3 days ago |
   ...
   ```

   If no tickets found:
   ```
   ## My Jira Tickets

   **User**: [display name]
   **Filter**: [filter description]

   No tickets found matching your criteria.
   ```

**Notes:**
- The query uses `assignee = currentUser()` which automatically filters for the current user
- Tickets are sorted by most recently updated first
- Maximum 50 tickets will be displayed at once
