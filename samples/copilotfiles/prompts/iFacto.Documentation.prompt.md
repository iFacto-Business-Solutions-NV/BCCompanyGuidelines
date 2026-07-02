---
description: "Generate or update project documentation — analyzes either full git history or current branch diff, verifying against code and JIRA issues."
agent: iFacto.Documentation
tools: [read, edit, search, execute, bc-code-intel/*, al-mcp-server/*, ms-dynamics-smb.al/*, mcp-server-atlassian-jira/*]
---

# Documentation Update

## Step 1: Determine Scope

Ask the user: **"Full history update or current branch only?"**

### If Full History:
1. Retrieve ALL recent commits (min 3-6 months):
   ```
   git log --pretty=format:"%H|%an|%ad|%s" --date=iso --reverse
   ```
2. Cross-reference EVERY commit with existing `CHANGELOG.md`

### If Current Branch:
1. Detect current branch: `git branch --show-current`
2. Get branch-specific commits: `git log main..HEAD --pretty=format:"%H|%an|%ad|%s" --date=iso` (or `master..HEAD`)
3. Only process commits in current branch NOT in main/master

## Step 2: JIRA Validation (MANDATORY)

- If NO JIRA issue reference detected: **STOP** and ask user to provide JIRA issue key
- Only proceed without JIRA if user explicitly confirms no issue exists
- Retrieve issue details via `mcp-server-atlassian-jira/jira_get`: summary, description, acceptance criteria
- Use JIRA issue summary as changelog entry text — NOT commit messages

## Step 3: Documentation Gap Analysis (Full History only)

For every commit, check:
- CHANGELOG.md completeness
- User-visible changes needing `Users/` docs
- Configuration requirements needing `Setup/` docs
- Testing requirements needing `Tests/` docs

## Step 4: Code Verification (MANDATORY)

Follow the `iFacto.documentation-generation` skill's code verification procedure:
1. `ms-dynamics-smb.al/al_symbolsearch` — enumerate objects
2. `ms-dynamics-smb.al/al_getdiagnostics` — build health
3. `read_file`, `grep_search` — verify field names, captions, procedures
4. Document ALL discrepancies

## Step 5: Execute All Updates

Follow the `iFacto.documentation-generation` skill for folder structure, templates, and changelog rules.

Create/update all relevant documentation files. Never ask whether to create — always do it.

## Constraints

- Never modify files outside `App/Docs/`
- Never modify `App/Docs/Instructions/` (read-only)
- JIRA keys always as clickable links: `[KEY-123](https://ifacto.atlassian.net/browse/KEY-123)`
- Use JIRA issue summary as changelog entry text — NOT commit messages
- Leave `[VERIFY]` only when info is truly unavailable
