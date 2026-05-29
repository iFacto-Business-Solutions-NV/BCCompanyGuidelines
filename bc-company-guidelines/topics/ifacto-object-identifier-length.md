---
id: "company/object-identifier-length"
title: "iFacto Object Identifier Length Limits"
domain: "company-standards"
tags: ["naming", "object-identifier", "length", "permissionset", "AL0305", "mandatory", "ifacto"]
difficulty: "beginner"
bc_version: "14+"
estimated_time: "5 minutes"
priority: "critical"
status: "mandatory"
added: "May 2026"
detection:
  enabled: true
  pattern: |
    (page|table|codeunit|report|query|xmlport|enum|interface|entitlement|pageextension|tableextension|reportextension|enumextension|permissionset|permissionsetextension)\s+\d+\s+"[^"]{31,}"
  check_type: "error"
relevance_signals:
  constructs: [page, table, codeunit, report, query, xmlport, enum, permissionset, pageextension, tableextension]
  keywords: [object identifier, name length, 30 characters, AL0305, permissionset name, Assignable, object name too long, identifier length]
  anti_pattern_indicators:
    - "object name exceeding 30 characters"
    - "permissionset name exceeding 20 characters with Assignable true"
    - "AL0305 compiler error"
  positive_pattern_indicators:
    - "object names within 30 character limit"
    - "permissionset names within 20 characters when Assignable"
applicable_object_types: [page, table, codeunit, report, query, xmlport, enum, permissionset, pageextension, tableextension, reportextension, enumextension, permissionsetextension]
relevance_threshold: 0.7
---

# iFacto Object Identifier Length Limits

**Company Standard - Critical Priority**

## What Makes This Company-Specific

While this is a platform constraint enforced by the AL compiler (AL0305), iFacto developers must be aware of these limits **upfront during object design** to avoid renaming objects after development has started. This is especially important given our PTE suffix convention which consumes characters from the budget.

## The Rule

### General Rule: Maximum 30 Characters

**ALL application object identifiers (names) MUST NOT exceed 30 characters.**

This applies to: Pages, Tables, Codeunits, Reports, Queries, XMLPorts, Enums, Interfaces, Entitlements, and all extension objects.

### Permission Set Exception: 20 Characters When Assignable

**Permission set object names are limited to 20 characters when `Assignable = true`.** When `Assignable = false`, the standard 30-character limit applies.

## Examples

### ✅ CORRECT - Object names within limits

```al
page 50100 "Default Dim. 2Key List PTE"
{
    // 27 characters - within 30-character limit
}
```

```al
permissionset 50100 "MyApp Basic PTE"
{
    Assignable = true;
    // 15 characters - within 20-character limit for assignable permission sets
}
```

```al
permissionset 50101 "MyApp Internal Objects PTE"
{
    Assignable = false;
    // 26 characters - within 30-character limit (not assignable)
}
```

### ❌ WRONG - Exceeding character limits

```al
page 50100 "Default Dimension 2Key List PTE"
{
    // 31 characters - EXCEEDS 30-character limit!
    // Compiler error AL0305: The length of the application object identifier
    // 'Default Dimension 2Key List PTE' cannot exceed 30 characters.
}
```

```al
permissionset 50100 "MyApp Basic Permissions PTE"
{
    Assignable = true;
    // 27 characters - EXCEEDS 20-character limit for assignable permission sets!
}
```

## Practical Tips

1. **Plan names early** - Account for the character limit during design phase
2. **Budget for PTE suffix** - The " PTE" suffix (4 characters including space) must be included in your character count for tableextension fields/procedures
3. **Use abbreviations wisely** - Prefer standard BC abbreviations (e.g., "Dim." for "Dimension", "Doc." for "Document", "No." for "Number")
4. **Check before creating** - Count characters in your object name before writing the object declaration

### Common BC Abbreviations

| Full Word | Abbreviation |
|-----------|-------------|
| Dimension | Dim. |
| Document | Doc. |
| Number | No. |
| Information | Info. |
| Configuration | Config. |
| Management | Mgt. |
| Registration | Reg. |
| Specification | Spec. |

## Why This Matters (iFacto Perspective)

- **AL0305 compiler errors** halt the build pipeline
- **Renaming objects after creation** can break references and requires careful refactoring
- **PTE suffix convention** already consumes characters from the budget - plan accordingly
- **Consistency** - abbreviated names should follow standard BC abbreviation patterns

## Enforcement

- The AL compiler enforces this with error **AL0305**
- bc-code-intel specialists should flag names approaching the limit during code review
- Names between 25-30 characters should receive a warning to leave room for future changes

## Related Guidelines

- [Naming Conventions](ifacto-naming-conventions.md) - General naming rules
- [TableExtension PTE Suffix](ifacto-tableextension-pte-suffix.md) - PTE suffix adds to character count
