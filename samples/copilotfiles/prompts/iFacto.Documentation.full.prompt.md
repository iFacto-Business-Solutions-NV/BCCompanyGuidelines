---
description: "Generate a full set of documentation based on all git commits, ensuring accuracy and completeness by verifying against code and JIRA issues."
agent: iFacto.Documentation
tools: [read, edit, search, execute, bc-code-intel/*, al-mcp-server/*, mcp-server-atlassian-jira/*]
---

# Full Documentation Update

Perform a complete documentation analysis and update for the `Docs` folder based on full git history.

## MANDATORY WORKFLOW

### Step 1: Complete Git History Analysis
1. Retrieve ALL recent commits (min 3-6 months):
   ```
   git log --pretty=format:"%H|%an|%ad|%s" --date=iso --reverse
   ```
2. Cross-reference EVERY commit with existing `CHANGELOG.md`
3. Use the `jira-enrichment` skill to retrieve JIRA details for ALL detected issue keys

### Step 2: Documentation Gap Analysis
For every commit, check:
- CHANGELOG.md completeness
- User-visible changes needing Users/ docs
- Configuration requirements needing Setup/ docs
- Testing requirements needing Tests/ docs

### Step 3: Code Verification (MANDATORY)
Follow the `documentation-generation` skill's code verification procedure:
1. `al_symbolsearch` — enumerate objects
2. `al_getdiagnostics` — build health
3. `read_file`, `grep_search` — verify field names, captions, procedures
4. Document ALL discrepancies

### Step 4: Execute All Updates
Follow the `documentation-generation` skill for folder structure, templates, and changelog rules.

Create/update all documentation files. Never ask whether to create — always do it.

## Constraints
- Never modify files outside `App/Docs/`
- Never modify `App/Docs/Instructions/` (read-only)
- JIRA keys always as clickable links: `[KEY-123](https://ifacto.atlassian.net/browse/KEY-123)`
- Use JIRA issue summary as changelog entry text — NOT commit messages
- Leave `[VERIFY]` only when info is truly unavailable
