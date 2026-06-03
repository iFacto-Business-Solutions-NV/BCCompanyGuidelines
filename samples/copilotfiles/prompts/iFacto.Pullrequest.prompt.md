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
**NEVER use `az repos pr create`** — use REST API via `az rest`.

### Encoding Rules (CRITICAL — prevents corruption of emojis and special chars)

**Windows (PowerShell):**
1. Build a hashtable with `title`, `description`, `sourceRefName`, `targetRefName`
2. Convert to JSON: `$json = $body | ConvertTo-Json -Depth 5`
3. Write to temp file **without BOM**: `[System.IO.File]::WriteAllText($tmpFile, $json, [System.Text.UTF8Encoding]::new($false))`
4. POST: `az rest --method POST --uri "https://dev.azure.com/{org}/{project}/_apis/git/repositories/{repoId}/pullrequests?api-version=7.1" --headers "Content-Type=application/json" --body "@$tmpFile"`
5. Clean up: `Remove-Item $tmpFile -Force -ErrorAction SilentlyContinue`

**macOS / Linux (bash/zsh):**
1. Use `python3 -c` to generate the JSON file (handles all escaping safely):
   `python3 -c "import json,sys; json.dump({'title':sys.argv[1],'description':sys.argv[2],'sourceRefName':sys.argv[3],'targetRefName':sys.argv[4]}, open(sys.argv[5],'w'), ensure_ascii=False)" "$TITLE" "$DESC" "$SOURCE" "$TARGET" "$TMPFILE"`
2. POST: `az rest --method post --uri "..." --headers "Content-Type=application/json" --body @"$TMPFILE"`
3. Clean up: `rm -f "$TMPFILE"`

**NEVER use:**
- PowerShell `Set-Content -Encoding UTF8` (adds BOM on PS5)
- Here-strings with emojis directly interpolated
- `echo` or `Write-Output` piped to file (encoding varies by shell)

### Authentication failure
If `az rest` returns 401/403 → tell the user: "Azure CLI token expired. Run `az login` to re-authenticate, then retry."

### Fallback to MCP tool
If `az rest` fails for ANY other reason (encoding error, network, unexpected response):
1. Inform the user: "REST API failed: [error]. Falling back to Azure DevOps MCP tool."
2. Use `repo_create_pull_request` from `azure-devops-mcp-server` with the same title and description
3. Continue with Step 6 as normal

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
