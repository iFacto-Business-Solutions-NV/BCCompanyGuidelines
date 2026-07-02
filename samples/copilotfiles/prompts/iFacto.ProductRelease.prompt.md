---
description: "Automates the release flow for Azure DevOps projects — creates release branches, updates pipeline config, pushes in dependency order."
agent: iFacto.ALCoder
tools: [read, edit, search, execute, al-mcp-server/*]
---

# Product Release Automation

Automates the release flow for Azure DevOps projects.

## Prerequisites
**CRITICAL**: Release version number (format: `xx.x`, e.g., `27.2`) MUST be provided before proceeding. Stop and show error if missing.

## Step 0: Pre-Release Health Check
Follow the `iFacto.al-build-validation` skill — diagnostics-only mode:
- Run `al_getdiagnostics` — list any AL errors
- Ask user whether to fix before proceeding or continue anyway

## Step 1: Create Release Branch
For each project folder: create and switch to `Release/{VERSION}` (e.g., `Release/27.2`)

## Step 2: Update azure-pipelines.yml
Add/update:
```yaml
artifactversion: '{VERSION}'
versionselect: 'latest'
appversiontemplate: "{VERSION}.[yyyyWW].*"
bak_filename: "$(global_bak_filename)_Release{VERSION_NO_DOT}"
trigger:
  branches:
    include:
      - Release/{VERSION}
```

## Step 3: Analyze Dependencies and Create Push Order
1. Read `app.json` files from each project's `/App` folder
2. Extract dependencies section
3. Build dependency tree
4. Create ordered list (no-dependency projects first, dependents after)
5. Output dependency-ordered list before proceeding

## Step 4: Commit and Push (in dependency order)
For each project in order:
```bash
git add .
git commit -m "Release {VERSION}: Update pipeline configuration for release branch"
git push origin Release/{VERSION}
```
Wait for push confirmation before next project.

## Step 5: Cleanup
Remove temporary files, backup files (`*.bak`, `*.tmp`), generated logs.

## Validation Checklist
- [ ] Release branch created for all projects
- [ ] Dependencies analyzed and push order determined
- [ ] `artifactversion`, `versionselect`, `appversiontemplate` set correctly
- [ ] `bak_filename` updated with `_Release{VERSION_NO_DOT}`
- [ ] Trigger branch changed to `Release/{VERSION}`
- [ ] Changes committed and pushed in dependency order
- [ ] Temporary files cleaned up
