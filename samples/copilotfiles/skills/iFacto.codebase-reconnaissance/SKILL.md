---
name: iFacto.codebase-reconnaissance
description: 'Discover existing objects, baseline errors, and extension points before writing or designing code. Use when: starting any AL implementation, designing solutions, reviewing code context. Replaces ad-hoc symbol/diagnostic discovery steps.'
---

# Codebase Reconnaissance

Standardized procedure to understand what exists before acting. Returns a structured context block.

## When to Use

- Before implementing a new feature (ALCoder step 1.5)
- Before writing a design document (SolutionDesigner step 3F)
- Before reviewing code for context (CodeReviewer step 1)
- Before any refactoring that touches multiple objects

## Procedure

### Step 1: Ensure Symbols Are Current

```
Run: ms-dynamics-smb.al/al_downloadsymbols
```
Ensures all dependency symbols are available for accurate search results.

### Step 2: Discover Existing Objects

```
Run: ms-dynamics-smb.al/al_symbolsearch
Query: keywords from the feature/task (object names, table names, field names, procedure names)
```

Search broadly — use multiple queries if needed:
- The primary object(s) being modified/created
- Related objects that may be affected
- Extension objects that may already exist for the target

Record:
- Objects that already exist (potential conflicts)
- Objects that can be extended
- Patterns used in similar existing code

### Step 3: Baseline Diagnostics

```
Run: ms-dynamics-smb.al/al_getdiagnostics
Scope: files you will touch or design around
```

Record:
- **Pre-existing errors** — do not compound them; flag critical ones to the user
- **Warnings** — note but don't necessarily fix
- **Clean files** — safe to modify without inheriting blame

### Step 4: Identify Extension Points

From Step 2 results, identify:
- Tables/pages marked `Extensible = true`
- Published events in codeunits you'll subscribe to
- Interfaces available for implementation
- Enums that can be extended (check extensibility property)

## Output Format

Return a structured summary to the calling agent:

```
## Reconnaissance Report

### Existing Objects Found
- [list with Type + ID + Name]

### Baseline Errors (pre-existing)
- [count] errors in [files] — [critical ones flagged]

### Available Extension Points
- [events, extensible objects, interfaces]

### Conflicts / Risks
- [name clashes, objects that don't exist but were expected, etc.]
```

## Failure Handling

- If `al_downloadsymbols` fails: proceed with stale symbols but warn the user
- If `al_symbolsearch` returns nothing: try alternative keywords, broader queries
- If `al_getdiagnostics` shows project-wide build failure: flag to user before proceeding — designing/coding on a broken baseline is risky
