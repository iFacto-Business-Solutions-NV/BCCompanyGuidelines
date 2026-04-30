---
description: "Documentation based on the changes you did — analyzes current branch diff against main/master."
agent: iFacto.Documentation
tools: [read, edit, search, execute, bc-code-intel/*, al-mcp-server/*, mcp-server-atlassian-jira/*]
---

# Branch Documentation Update

Perform documentation updates based on current branch changes only (diff against main/master).

## MANDATORY WORKFLOW

### Step 1: Branch Diff Analysis
1. Detect current branch: `git branch --show-current`
2. Get branch-specific commits: `git log main..HEAD --pretty=format:"%H|%an|%ad|%s" --date=iso` (or `master..HEAD`)
3. Only process commits in current branch NOT in main/master

### JIRA Requirement (STRICT)
- If NO JIRA issue reference detected in commits: **STOP** and ask user to provide JIRA issue key
- Only proceed without JIRA if user explicitly confirms no issue exists
- Use the `jira-enrichment` skill to validate and retrieve issue details
- Use JIRA issue summary as changelog entry text — NOT commit messages

### Step 2: Code Verification (MANDATORY)
Follow the `documentation-generation` skill's code verification procedure:
1. `al_symbolsearch` — enumerate objects
2. `al_getdiagnostics` — build health
3. `read_file`, `grep_search` — verify field names, captions, procedures

### Step 3: Execute All Updates
Follow the `documentation-generation` skill for folder structure, templates, and changelog rules.

Create/update all relevant documentation files.

## Constraints
- Never modify files outside `App/Docs/`
- Never modify `App/Docs/Instructions/` (read-only)
- JIRA keys always as clickable links
- Leave `[VERIFY]` only when info is truly unavailable
