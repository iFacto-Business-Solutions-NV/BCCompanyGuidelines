---
id: "company/ifacto-single-object-per-file"
title: "iFacto Code Organization Standards (Company Delta)"
domain: "company-standards"
tags: ["file-organization", "code-structure", "mandatory", "ifacto"]
difficulty: "beginner"
bc_version: "14+"
estimated_time: "10 minutes"
priority: "high"
status: "mandatory"
added: "October 2025"
---

# iFacto Code Organization Standards (Company Delta)

**Company Standard - HIGH PRIORITY**  
**Status:** MANDATORY  
**Added:** October 2025  

## What Makes This Company-Specific

Standard BC development allows flexibility in file organization. **iFacto mandates** strict single-object-per-file rule with NO exceptions.

## The Rule: Single Object Per File

### Absolute Requirement

**Each `.al` file MUST declare EXACTLY ONE AL object or object extension.**

No bundling. No exceptions. Regardless of:
- Object size
- Perceived relation between objects
- Convenience
- "They're always used together"

### Why It Matters (iFacto Perspective)

- **Diff clarity** - Changes isolated to specific objects
- **Merge conflicts** - Reduced in team collaboration
- **Code ownership** - Clear responsibility per file
- **AI/tooling** - Better symbol resolution
- **Code review** - Precise scope per file
- **Upgrade tracking** - Cleaner lifecycle management

### Filename Pattern

**Required Format:** `<ObjectType> <ID> <Pascal Name><Suffix>.al`

```
✅ CORRECT Examples:
Table 50100 Customer Extension.al
TableExt 50101 Customer Ext.al
Page 50102 Customer Card Extension.al
PageExt 50103 Customer Card Ext.al
Codeunit 50104 Customer Management.al
Codeunit 50105 Customer CalculateScore Meth.al
Codeunit 50106 Customer Management Tests.al
Enum 50107 Rating Level.al
Interface 50108 ICustomer Validator.al

❌ WRONG Examples:
CustomerExtension.al                    # Missing object type and ID
customer_extension.al                   # Wrong case
50100.al                               # Missing object type and name
CustomerTablesAndPages.al              # Implies multiple objects
customer-extension.al                  # Wrong case and separator
```

### Alternative Format (Team Consistency Required)

If your team prefers: `ObjectType.ID.NameSuffix.al`

```
Table.50100.CustomerExtension.al
Codeunit.50105.CustomerCalculateScoreMeth.al
```

**Important:** Pick ONE format and use it consistently across the entire project.

## Disallowed Patterns

### ❌ NEVER: Multiple Objects in One File

```al
// WRONG: Two objects in same file
table 70000 "Mail Queue" 
{ 
    // Fields & logic
}

codeunit 70001 "Mail Queue Processor" 
{ 
    // Processing logic
}
```

**Why this is forbidden:**
- Git diffs become confusing (which object changed?)
- Merge conflicts affect both objects
- Code review unclear (reviewing which object?)
- Object ID management complicated
- Violates single responsibility at file level

### ✅ CORRECT: Split into Separate Files

**File 1:** `Table 70000 Mail Queue.al`
```al
table 70000 "Mail Queue"
{
    Caption = 'Mail Queue';
    DataClassification = CustomerContent;
    
    fields
    {
        field(1; "Entry No."; Integer)
        {
            Caption = 'Entry No.';
        }
        // More fields...
    }
}
```

**File 2:** `Codeunit 70001 Mail Queue Processor.al`
```al
codeunit 70001 "Mail Queue Processor"
{
    procedure ProcessQueue()
    var
        MailQueue: Record "Mail Queue";
    begin
        // Processing logic
    end;
}
```

## Special Cases

### Test Codeunits

**Append "Tests" suffix:**

```
✅ CORRECT:
Codeunit 50190 Customer Management Tests.al
Codeunit 50191 Sales Order Processing Tests.al

❌ WRONG:
Codeunit 50190 CustomerTests.al          # Unclear what's being tested
Codeunit 50190 Customer Test.al          # "Test" not "Tests"
```

### Meth Codeunits (iFacto Pattern)

**Include "Meth" in name:**

```
✅ CORRECT:
Codeunit 50111 Customer CalculateScore Meth.al
Codeunit 50112 Sales Order ApplyDiscount Meth.al

❌ WRONG:
Codeunit 50111 Customer Meth.al          # Too generic
Codeunit 50111 CalculateScore.al         # Missing "Meth" indicator
```

### Extensions

**Clear extension indication:**

```
✅ CORRECT:
TableExt 50101 Customer Ext.al
PageExt 50103 Customer Card Ext.al
EnumExt 50105 Payment Method Ext.al

❌ WRONG:
TableExt 50101 Customer.al               # Unclear if extension
Table 50101 Customer Extension.al        # Wrong object type
```

## Migration from Legacy Multi-Object Files

If you have existing files with multiple objects:

### Step 1: Identify Offenders
```powershell
# Search for files with multiple object declarations
Get-ChildItem -Path ".\Src" -Filter "*.al" -Recurse | 
    Select-String -Pattern "^(table|page|codeunit|report|enum|interface)" | 
    Group-Object Path | 
    Where-Object { $_.Count -gt 1 }
```

### Step 2: Split Files

1. Create new files with proper naming
2. Move each object to its own file
3. Keep IDs and names identical
4. Delete old multi-object file

### Step 3: Validate

1. Recompile project
2. Run all tests
3. Check for duplicate symbol warnings
4. Verify no references broken

### Step 4: Commit

Commit in logical batches grouped by domain for easier review.

## AI Specialist Enforcement

When specialists encounter requests to add objects:

**User asks to add another object to existing file:**
```
"⚠️ According to iFacto standards, each .al file must contain exactly ONE object.

I'll create a new file instead:
Recommended filename: [ObjectType] [ID] [Name].al

This ensures:
✅ Clean git diffs
✅ Reduced merge conflicts  
✅ Clear code ownership
✅ Better maintainability"
```

**User creates file with multiple objects:**
```
"⚠️ This file contains multiple AL objects, which violates iFacto's single-object-
per-file standard. 

Please split into separate files:
1. [Object 1] → [Recommended filename 1]
2. [Object 2] → [Recommended filename 2]

This is mandatory for all iFacto BC projects."
```

## Code Review Checklist

- [ ] Does each .al file contain exactly ONE object/extension?
- [ ] Does filename follow pattern: `<ObjectType> <ID> <Name><Suffix>.al`?
- [ ] Are test codeunits named with "Tests" suffix?
- [ ] Are Meth codeunits named with "Meth" in the name?
- [ ] Are extensions clearly marked (TableExt, PageExt, etc.)?
- [ ] No commented-out second object remnants?
- [ ] Tests updated for any moved objects?

## Related Guidelines

- [Naming Conventions](ifacto-naming-conventions.md) for object naming and file naming standards
- [Meth Codeunit Pattern](ifacto-meth-codeunit-pattern.md) for Meth codeunit file naming
- [Enum Patterns](ifacto-enum-patterns.md) for enum file naming

---

**This is an iFacto-specific enforcement.** While BC supports multiple objects per file technically, iFacto **prohibits** this practice for all projects to ensure maintainability and team collaboration.
