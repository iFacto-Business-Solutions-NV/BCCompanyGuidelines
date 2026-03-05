---
id: "company/ifacto-no-ps1-in-app"
title: "iFacto PowerShell Test Scripts Not Allowed in Main App"
domain: "company-standards"
tags: ["powershell", "ps1", "test-scripts", "app-folder", "project-structure", "mandatory", "ifacto"]
difficulty: "beginner"
bc_version: "14+"
estimated_time: "5 minutes"
priority: "critical"
status: "mandatory"
added: "February 2026"
relevance_signals:
  keywords: [powershell, ps1, test scripts, app folder, project structure, deployment scripts, script location, production app]
  anti_pattern_indicators:
    - "PowerShell script in App folder"
    - "ps1 file in production app directory"
    - "test script outside Test folder"
  positive_pattern_indicators:
    - "ps1 scripts in Test folder only"
    - "PowerShell scripts outside App directory"
    - "test scripts organized in Test project"
relevance_threshold: 0.4
---

# iFacto PowerShell Test Scripts – Not Allowed in Main App

**Company Standard - Critical Priority**

## What Makes This Company-Specific

Standard BC project structures do not enforce rules about where PowerShell scripts live. **iFacto mandates** that `.ps1` files created for testing purposes must **never** be placed in the `App/` folder (the production app). They must only exist in the `Test/` folder (the test-app).

## The Rule

**NO `.ps1` files are allowed inside the `App/` folder.**

PowerShell test scripts belong exclusively in the `Test/` folder.

### Repository Structure Context

```
<RepoRoot>/
  App/          ← Main app (business logic, production code) — NO .ps1 files allowed
  Test/         ← Test-app (test codeunits, test utilities, PS1 scripts) — .ps1 files go here
```

## Examples

### ✅ CORRECT – PS1 script in Test folder

```
<RepoRoot>/
  App/
    src/
      Codeunit 50100 Customer Management.al
  Test/
    src/
      Codeunit 80100 Customer Management Tests.al
    scripts/
      Run-CustomerTests.ps1          ← Correct location
      Setup-TestData.ps1             ← Correct location
```

### ❌ WRONG – PS1 script in App folder

```
<RepoRoot>/
  App/
    src/
      Codeunit 50100 Customer Management.al
    scripts/
      Run-CustomerTests.ps1          ← VIOLATION: .ps1 in App/
      Setup-TestData.ps1             ← VIOLATION: .ps1 in App/
  Test/
    src/
      Codeunit 80100 Customer Management Tests.al
```

## Why This Matters (iFacto Perspective)

- **PS1 test scripts are development/testing utilities**, not production code
- **Including them in the main app bloats the production package** shipped to customers
- **They may expose internal testing logic or credentials** to customers
- **Enforces clean separation** between production code and test tooling
- **Cleaner CI/CD pipelines** – production builds should never package test utilities

## Detection & Enforcement

**Pull Request Rule:** If a PR adds or modifies a `.ps1` file inside the `App/` folder, flag it as a violation.

**Automated check example (PowerShell):**

```powershell
# Flag any .ps1 files under App/
$violations = Get-ChildItem -Path "App/" -Filter "*.ps1" -Recurse
if ($violations) {
    Write-Error "VIOLATION: .ps1 files found in App/ folder. Move them to Test/."
    $violations | ForEach-Object { Write-Error "  - $($_.FullName)" }
    exit 1
}
```

**Code review checklist:**
- [ ] No `.ps1` files added or modified inside `App/`
- [ ] Any new test scripts placed under `Test/`

## Exceptions

**None.** There is no valid reason for a `.ps1` test script to live in the production `App/` folder. If a PowerShell script is needed for production deployment (e.g., install scripts referenced by the app manifest), it must be reviewed and explicitly approved by the tech lead — but test scripts are never an exception.

## Related Guidelines

- [iFacto Code Organization Standards](ifacto-single-object-per-file.md) – Single object per file rule
- [iFacto Naming Conventions](ifacto-naming-conventions.md) – Naming standards for files and objects
