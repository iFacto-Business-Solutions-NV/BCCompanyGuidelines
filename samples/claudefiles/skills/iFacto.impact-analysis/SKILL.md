---
name: iFacto.impact-analysis
description: 'Analyze what breaks if an object, field, or procedure changes. Use when: approving risky changes in review, before refactoring, evaluating design trade-offs. Builds dependency tree and identifies transitive risk.'
---

# Impact Analysis

Structured procedure to answer: "What breaks if I change this?"

## When to Use

- **CodeReviewer**: Before approving changes to shared objects (tables, interfaces, events)
- **ALCoder**: Before refactoring or renaming anything with external consumers
- **SolutionDesigner**: Evaluating trade-offs between extending vs. creating new objects

## Input

Provide one or more of:
- Object name (table, codeunit, page, enum)
- Field name (with parent table)
- Procedure/event name (with parent codeunit)
- Interface name

## Procedure

### Step 1: Find Direct References

```
Run: al-mcp-server/al_find_references
Target: the object/field/procedure being changed
```

Record all direct consumers:
- Table extensions referencing the table
- Pages showing the field
- Codeunits calling the procedure
- Event subscribers
- Permission sets granting access

### Step 2: Build Dependency Tree

For each direct consumer found in Step 1:
- Is it also consumed by others? (check one level deeper)
- Is it part of a published app (vs. same project)?
- Is it in a test app (lower risk) or production app (higher risk)?

Categorize:
- **Same-project** — you control it, can fix it
- **Cross-project dependency** — breaking change requires coordination
- **External/AppSource** — cannot fix; must maintain backward compatibility

### Step 3: Diagnostics on Affected Files

```
Run: ms-dynamics-smb.al/al_getdiagnostics
Scope: files identified as direct consumers in Step 1
```

Check: would the proposed change introduce errors in these files?

### Step 4: Test Coverage Check

Search for test codeunits that reference the target:
```
Run: al-mcp-server/al_find_references
Target: same object, filtered to Test* files
```

Or:
```
Run: ms-dynamics-smb.al/al_symbolsearch
Query: "Test" + object name
```

Flag: objects with zero test coverage that will be affected.

## Output Format

```
## Impact Analysis: [Object/Field/Procedure Name]

### Direct Consumers ([count])
| Consumer | Type | Project | Risk |
|----------|------|---------|------|
| ... | TableExt/Page/Codeunit | Same/Cross/External | Low/Med/High |

### Transitive Risk
- [objects one level deeper that may break]

### Breaking Change? YES / NO / CONDITIONAL
- YES: [what breaks and why]
- CONDITIONAL: [breaks only if X property changes]
- NO: [change is additive/safe]

### Test Coverage
- Covered: [count] test codeunits
- Uncovered consumers: [list]

### Recommendation
- [Proceed / Proceed with caution / Coordinate with dependent teams / Do not change]
```

## Edge Cases

- **No references found**: The object may be unused (candidate for removal) or references may be in unpublished symbols. State this explicitly.
- **Circular references**: Flag the cycle, don't recurse infinitely.
- **Interface changes**: Always HIGH risk — all implementations must be updated simultaneously.
