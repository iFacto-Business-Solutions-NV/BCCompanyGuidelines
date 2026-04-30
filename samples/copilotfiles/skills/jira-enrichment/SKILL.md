---
name: jira-enrichment
description: 'Discover JIRA MCP tools, validate issues, extract structured context, and set issue status to Confirmed when starting work. Use when: creating pull requests, designing solutions, writing documentation that references JIRA issues, validating requirements against implementation.'
---

# JIRA Issue Enrichment

## When to Use
- Pull request creation requiring JIRA context
- Solution design referencing JIRA requirements
- Documentation updates linked to JIRA issues
- Requirement validation against implementation

## Procedure

### Step 1: Discover JIRA MCP Tools
Search available tools for any containing `jira` in the name (`mcp-server-atlassian-jira/*`).
- If tools not found: attempt `MCP: Start Server` auto-start
- If still unavailable: **STOP** — show error: "JIRA MCP server is not available. Cannot proceed without JIRA integration."
- **NEVER simulate or fabricate JIRA data**

### Step 2: Validate Issue Exists
Call the JIRA GET tool:
```
Path: /rest/api/3/issue/{issueIdOrKey}
```
- If issue not found: STOP and ask user for correct issue key
- If no issue key provided: STOP and ask user — JIRA reference is MANDATORY

### Step 3: Check Issue Status

Read the `status` field from the Step 2 response and classify it:

| Category | Examples | Meaning |
|----------|---------|---------|
| **Unstarted** | Open, To Do, New, Backlog | No one has claimed this issue yet |
| **Confirmed** | Confirmed | Developer has claimed the issue — work may proceed |
| **In Progress** | In Progress, In Review, In Testing | Work is already active |
| **Done** | Resolved, Closed, Done | Issue is finished |

Return the status and category as part of the enriched context.  
**Do NOT transition the issue here** — that only happens after user confirmation (see below).

---

## Setting Issue to "Confirmed" (After User Confirmation)

Call this procedure **only after** the agent has asked the user to confirm they want to start working on the issue **and the user has confirmed**.

### When to trigger
- Status category is **Unstarted** → agent asks user: _"Do you want to set [KEY] to Confirmed and start working on it?"_ → wait for answer → only proceed if user confirms
- Status category is **Confirmed** or **In Progress** → proceed directly, no transition needed
- Status category is **Done** → warn the user: _"⚠️ [KEY] is already marked as Done. Are you sure you want to work on this issue?"_ → wait for explicit confirmation

### Transition procedure
1. Get available transitions:
   ```
   GET /rest/api/3/issue/{issueIdOrKey}/transitions
   ```
2. Find the transition ID where `"name"` is `"Confirmed"` (or similar: `"Confirm"`, `"Start"`)
3. Apply the transition:
   ```
   POST /rest/api/3/issue/{issueIdOrKey}/transitions
   Body: { "transition": { "id": "{transitionId}" } }
   ```
4. If transition fails: **warn the user** (do not block the work):
   > "⚠️ Could not set [KEY] to Confirmed automatically. Please set it manually in JIRA so colleagues know you are working on it."

**Why this matters:** Confirmed prevents other developers from picking up the same issue, and gives consultants a clear view of who is working on what.

---

### Step 4: Extract Structured Context
From the JIRA response, extract:
- **Summary**: Issue title
- **Description**: Full description text
- **Acceptance Criteria**: From description or custom field
- **Comments**: Recent comments (last 10)
- **Priority**: Issue priority level
- **Labels**: All labels
- **Status**: Current workflow status
- **Related Issues**: Linked issues (blocks, is-blocked-by, relates-to)

### Step 5: Format for Consumer
Return structured context:
```
## JIRA Context: {ISSUE-KEY}
**Summary**: {summary}
**Priority**: {priority}
**Status**: {status}
**Labels**: {labels}

### Requirements
{description + acceptance criteria}

### Recent Activity
{last 3-5 comments summarized}

### Related Issues
{linked issues with relationship type}
```

## JIRA Link Format
Always format JIRA keys as clickable links:
```
[KEY-123](https://ifacto.atlassian.net/browse/KEY-123)
```

## Adding Comments to JIRA
When writing back to JIRA (e.g., PR creation):
1. Use the discovered JIRA POST tool
2. Path: `/rest/api/3/issue/{issueIdOrKey}/comment`
3. Convert markdown to Atlassian Document Format (ADF)
4. If comment fails: log error but don't fail the parent operation

## Important
- **NEVER answer from general knowledge** — all JIRA context must come from MCP tools
- **NEVER fabricate issue keys, summaries, or content**
- If JIRA MCP is unavailable, clearly communicate this to the user
