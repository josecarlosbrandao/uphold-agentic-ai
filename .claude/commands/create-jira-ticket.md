---
allowed-tools: mcp__atlassian__atlassianUserInfo, mcp__atlassian__getAccessibleAtlassianResources, mcp__atlassian__getVisibleJiraProjects, mcp__atlassian__getJiraProjectIssueTypesMetadata, mcp__atlassian__createJiraIssue, mcp__atlassian__getJiraIssue, AskUserQuestion
description: Create a new Jira ticket and assign it to yourself
---

## Your task

Create a new Jira ticket and automatically assign it to the current user.

**Steps:**

1. Get the current user's information:
   - Use `mcp__atlassian__atlassianUserInfo` to get the current user's account ID and display name

2. Get the Atlassian cloud ID:
   - Use `mcp__atlassian__getAccessibleAtlassianResources` to get the cloud ID

3. Get available projects:
   - Use `mcp__atlassian__getVisibleJiraProjects` with action: "create" to get projects where user can create issues
   - Display the list of available projects

4. Use AskUserQuestion to ask which project to create the ticket in:
   - Show the list of available projects from step 3
   - Let user select the project key (e.g., PROJ, DEV, ENG)

5. Get available issue types for the selected project:
   - Use `mcp__atlassian__getJiraProjectIssueTypesMetadata` with the project key from step 4
   - Display the available issue types (Task, Story, Bug, Epic, etc.)

6. Use AskUserQuestion to ask for the issue type:
   - **Task** - A task to be done
   - **Story** - A user story
   - **Bug** - A bug to be fixed
   - Other types available in the project

7. Use AskUserQuestion to ask for the required fields:
   - **Title/Summary**: Ask "What is the title of this ticket?" (required)
   - **Description**: Ask "Provide a description for this ticket (supports Markdown)" (required)

8. Use AskUserQuestion to ask for optional fields:

   **Parent Ticket** (optional):
   - Ask: "Do you want to link this to a parent ticket?"
   - Options:
     - **No** - This is a standalone ticket
     - **Yes** - Let me specify a parent ticket (e.g., PROJ-123)
   - If Yes, ask for the parent ticket ID

   **Labels** (optional):
   - Ask: "Do you want to add labels to this ticket?"
   - Options:
     - **No** - No labels
     - **Yes** - Let me specify labels (e.g., maintenance, phase-1, backend)
   - If Yes, ask for comma-separated labels (e.g., "maintenance, phase-2, urgent")

   **Priority** (optional):
   - Ask: "What priority should this ticket have?"
   - Options:
     - **Default** - Use project default priority
     - **Highest** - Critical/Blocker
     - **High** - Important
     - **Medium** - Normal
     - **Low** - Minor
     - **Lowest** - Trivial

9. Build the additional_fields object:
   ```javascript
   additional_fields = {}

   // Add labels if provided
   if (labels) {
     additional_fields.labels = [array of label strings]
   }

   // Add priority if provided
   if (priority !== "Default") {
     additional_fields.priority = { name: priority }
   }
   ```

10. Create the Jira issue:
    - Use `mcp__atlassian__createJiraIssue` with:
      - cloudId: from step 2
      - projectKey: from step 4
      - issueTypeName: from step 6
      - summary: title from step 7
      - description: description from step 7
      - assignee_account_id: current user's account ID from step 1
      - parent: parent ticket ID from step 8 (if provided)
      - additional_fields: from step 9 (if any)

11. Get the created issue details:
    - Use `mcp__atlassian__getJiraIssue` with the issue ID/key returned from step 10
    - Extract the issue URL and other details

12. **Final feedback**: Display a success message:
    ```
    ## Jira Ticket Created ✅

    - **Key**: PROJ-123
    - **URL**: [PROJ-123](https://yourcompany.atlassian.net/browse/PROJ-123)
    - **Title**: [title]
    - **Type**: [Task/Story/Bug]
    - **Project**: [project name]
    - **Assignee**: [your name] (you)
    - **Priority**: [priority or "Default"]
    - **Labels**: [labels or "None"]
    - **Parent**: [parent ticket or "None"]
    - **Status**: To Do (or default status)

    The ticket has been created and assigned to you.
    ```

**Important Notes:**
- The ticket is automatically assigned to you (current user)
- Only title and description are required
- Parent ticket, labels, and priority are optional
- Description supports Markdown formatting
- If creating a subtask, you must specify a parent ticket
- The ticket will be created with the project's default status (usually "To Do" or "Backlog")

**Error Handling:**
- If the parent ticket ID is invalid or doesn't exist, show an error and don't create the ticket
- If the project doesn't allow creating the specified issue type, show available types
- If labels are not enabled in the project, skip the labels field
