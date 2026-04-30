---
description: "Automates update of platform, application, and version numbers in all BC app.json files within the workspace."
agent: iFacto.ALCoder
tools: [read, edit, search, execute, al-mcp-server/*]
---

# Product Version / Application / Platform Update

ONLY update fields explicitly requested in ALL `app.json` files (including `/App` and `/Test` subfolders) and commit with standardized message.

## Critical Rules
- Only update what is explicitly requested (platform / application / version)
- NuGet update: SKIP unless user explicitly requests it
- DO NOT update Microsoft publisher dependencies unless explicitly requested

## Prerequisites
Ask for values ONLY for the fields explicitly requested:
- **Platform**: format `MAJOR.MINOR` (e.g., `27.0`)
- **Application**: format `MAJOR.MINOR` (e.g., `27.2`)
- **Version**: format `MAJOR.MINOR.PATCH.BUILD` (e.g., `27.2.0.0`)
- **NuGet** (only if requested): format `MAJOR.MINOR-SUFFIX` (e.g., `27.2-RLS`), default suffix `RLS`

## Workflow

### Step 1: Validate Requested Values
Validate format for each requested field. Stop and ask if format invalid.

### Step 2: Find All app.json Files
Search `**/app.json` — include `/App` and `/Test` subfolders. List all found files.

### Step 3: Update Fields (only requested ones)
- Only update requested fields in each `app.json`
- DO NOT modify Microsoft publisher dependencies
- Preserve ALL JSON formatting and indentation
- Show before/after comparison for updated fields only

### Step 4: Update nuget.json (ONLY IF EXPLICITLY REQUESTED)
Find `**/nuget.json`. Update `"version": "MAJOR.MINOR-SUFFIX"`. Show before/after.

### Step 5: Commit Changes
Stage changed files. Build commit message containing ONLY updated fields:
- Platform only: `Update to platform 27.0`
- All three: `Update to platform 27.0, application 27.2, version 27.2.0.0`
- With nuget: `Update to platform 27.0, application 27.2, version 27.2.0.0, nuget 27.2-RLS`

### Step 6: Verify Build Health
Follow the `al-build-validation` skill:
- Run `al_getdiagnostics` to check for version conflicts or platform-incompatible API usage
- Report errors to user — do NOT silently proceed if build is broken

## Error Handling
- No `app.json` found → stop with clear error
- Invalid format → stop and ask for correction
- Git commit failure → report and suggest manual resolution
- Build errors after update → report diagnostics to user
