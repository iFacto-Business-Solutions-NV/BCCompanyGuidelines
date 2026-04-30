---
description: "Apply all steps to create a pull request for your new developments — includes JIRA validation, documentation, and Azure DevOps PR creation."
agent: iFacto.Documentation
tools: [read, search, execute, bc-code-intel/*, al-mcp-server/*, mcp-server-atlassian-jira/*, azure-devops-mcp-server/*]
---

# Pull Request Documentation

Creates comprehensive pull request documentation with 5 mandatory sections.

## Step 1: JIRA Validation (MANDATORY FIRST)
Use the `jira-enrichment` skill:
1. Validate JIRA issue key is provided — **STOP and ask if missing** (NEVER fabricate)
2. Retrieve issue details: summary, description, acceptance criteria
3. Compare PR changes to requirements: ✅ met / ⚠️ partial / ❌ not met / 🔍 scope creep
4. **Get user confirmation before proceeding**

## Step 2: Specialist Consultation
Consult **taylor-docs** via `mcp_bc-code-intel_ask_bc_expert` for proper PR documentation structure and BC-specific patterns.

## Step 3: Change Detection
- Detect uncommitted changes + branch-specific commits (since branch created from main/master)
- Run `al_symbolsearch` to build accurate object inventory
- Follow the `al-build-validation` skill (diagnostics-only) to capture build status
- If NO test files found: flag as missing automated tests — **NEVER fabricate test content**

## Step 4: Generate PR Description
**Format restrictions:**
- ✅ Headings, bullet lists, emoticons, bold text
- ❌ NO code blocks, NO tables with pipes
- **HARD LIMIT: 3800 characters maximum**

**Structure:**
```
# [Emoticon] [JIRA-ISSUE]: [Title]
## Summary
## Changes
## Testing
## Deployment
## Assessment (star ratings as list)
---
Commit info
```

## Step 5: Create PR via Azure REST API
**NEVER use `az repos pr create`** — use REST API:
```powershell
# Generate description → create JSON body file → POST via az rest
# POST to /_apis/git/repositories/{repoId}/pullrequests?api-version=7.1
# Clean up temp JSON file
```

## Step 6: Update JIRA Issue
If JIRA was validated: add comment with PR link + description (excluding Deployment and Assessment sections).
- Convert markdown to Atlassian Document Format (ADF)
- If comment fails: log error but don't fail PR creation

## 5 Mandatory Documentation Sections
1. **JIRA Integration** — link, summary, business context
2. **Code Changes** — what changed, why, how
3. **Testing** — automated tests, manual procedures, edge cases (⚠️ WARNING if no automated tests)
4. **Setup Guide** — prerequisites, steps, configuration
5. **Architecture Review** — honest 1-5 star ratings: Code Architecture, Code Quality, Performance, Maintainability, Extensibility

## Quality Standards
- Under 3800 characters (verify before creating PR)
- No placeholder/simulated content
- Accurate test state representation
- Honest architecture ratings (not everything is 5 stars)
