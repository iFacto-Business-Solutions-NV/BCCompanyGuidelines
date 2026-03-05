---
id: "company/tableextension-pte-suffix"
title: "iFacto TableExtension PTE Suffix Standard"
domain: "company-standards"
tags: ["tableextension", "naming", "pte", "suffix", "mandatory", "ifacto"]
difficulty: "beginner"
bc_version: "14+"
estimated_time: "10 minutes"
priority: "critical"
status: "mandatory"
added: "February 2026"
detection:
  enabled: true
  pattern: |
    (field\s*\(\s*\d+\s*;\s*"[^"]*(?<!PTE)"\s*;|procedure\s+\w+(?<!PTE)\s*\()
  check_type: "error"
relevance_signals:
  constructs: [tableextension, field, procedure]
  keywords: [PTE suffix, tableextension fields, custom fields, naming standard, iFacto suffix, extension fields, field naming, procedure naming]
  anti_pattern_indicators:
    - "field without PTE suffix in tableextension"
    - "procedure without PTE suffix in tableextension"
    - "custom field missing PTE identifier"
  positive_pattern_indicators:
    - "field name ending in PTE"
    - "procedure name ending in PTE"
    - "all custom members use PTE suffix"
applicable_object_types: [tableextension]
relevance_threshold: 0.7
---

# iFacto TableExtension PTE Suffix Standard

**Company Standard - Critical Priority**

## What Makes This Company-Specific

While standard BC development allows any naming convention for extension fields/procedures, **iFacto enforces a MANDATORY "PTE" suffix** on all new fields and procedures in TableExtension objects. This is a strict company-wide requirement for clear identification of customizations and upgrade safety.

## The Rule

**CRITICAL RULE:** All new fields and procedures in TableExtensions MUST have "PTE" as a suffix.

### Applies To
- **TableExtension objects only** (not base Tables)
- **New fields** added to existing tables via TableExtension
- **New procedures** (methods/functions) added via TableExtension

### Positioning & Formatting
- **Suffix position:** "PTE" must come AFTER the field/procedure name
- **Case sensitive:** Use exact capitalization "PTE" (not "pte" or "Pte")
- **Spacing:** Use a space before "PTE" in quoted identifiers

### What DOESN'T Require PTE?
- ❌ Fields and procedures in base Tables (not extensions)
- ❌ Fields and procedures in other object types (Pages, Codeunits, Reports, etc.)
- ❌ Modifications to existing fields/procedures (only NEW ones need PTE)

## Examples

### ✅ CORRECT - TableExtension with PTE Suffix

**Example 1: Fields with PTE suffix**
```al
tableextension 50100 "Customer Extension PTE" extends Customer
{
    fields
    {
        field(50100; "Loyalty Points PTE"; Integer) 
        { 
            Caption = 'Loyalty Points';
            DataClassification = CustomerContent;
        }
        
        field(50101; "VIP Status PTE"; Boolean) 
        { 
            Caption = 'VIP Status';
            DataClassification = CustomerContent;
        }
        
        field(50102; "Last Reward Date PTE"; Date)
        {
            Caption = 'Last Reward Date';
            DataClassification = CustomerContent;
        }
    }
}
```

**Example 2: Procedures with PTE suffix**
```al
tableextension 50100 "Customer Extension PTE" extends Customer
{
    fields
    {
        field(50100; "Loyalty Points PTE"; Integer) { }
    }
    
    procedure CalculateDiscountPTE(): Decimal
    var
        DiscountPercentage: Decimal;
    begin
        if Rec."Loyalty Points PTE" > 1000 then
            DiscountPercentage := 10
        else if Rec."Loyalty Points PTE" > 500 then
            DiscountPercentage := 5;
            
        exit(DiscountPercentage);
    end;
    
    procedure UpdateLoyaltyPointsPTE(Points: Integer)
    begin
        Rec."Loyalty Points PTE" += Points;
        Rec.Modify(true);
    end;
}
```

**Example 3: Complete TableExtension**
```al
tableextension 50150 "Item Extension PTE" extends Item
{
    fields
    {
        field(50150; "Supplier Rating PTE"; Integer)
        {
            Caption = 'Supplier Rating';
            DataClassification = CustomerContent;
            MinValue = 1;
            MaxValue = 5;
        }
        
        field(50151; "Last Quality Check PTE"; Date)
        {
            Caption = 'Last Quality Check';
            DataClassification = CustomerContent;
        }
    }
    
    procedure ValidateQualityPTE(): Boolean
    var
        QualityPassedLbl: Label 'Quality check passed for item %1';
    begin
        if Rec."Supplier Rating PTE" >= 3 then begin
            Message(QualityPassedLbl, Rec."No.");
            exit(true);
        end;
        
        exit(false);
    end;
}
```

### ❌ WRONG - Missing PTE Suffix

**Example 1: Missing PTE on fields**
```al
tableextension 50100 "Customer Extension" extends Customer
{
    fields
    {
        field(50100; "Loyalty Points"; Integer) { }  // ❌ Missing PTE suffix
        field(50101; "VIP Status"; Boolean) { }       // ❌ Missing PTE suffix
    }
}
```

**Example 2: Missing PTE on procedures**
```al
tableextension 50100 "Customer Extension PTE" extends Customer
{
    fields
    {
        field(50100; "Loyalty Points PTE"; Integer) { }
    }
    
    procedure CalculateDiscount(): Decimal  // ❌ Missing PTE suffix
    begin
        // Logic here
    end;
    
    procedure UpdateLoyaltyPoints(Points: Integer)  // ❌ Missing PTE suffix
    begin
        Rec."Loyalty Points PTE" += Points;
    end;
}
```

**Example 3: Wrong capitalization**
```al
tableextension 50100 "Customer Extension PTE" extends Customer
{
    fields
    {
        field(50100; "Loyalty Points pte"; Integer) { }  // ❌ Wrong case - should be "PTE"
        field(50101; "VIP Status Pte"; Boolean) { }       // ❌ Wrong case - should be "PTE"
    }
}
```

**Example 4: Wrong positioning (prefix instead of suffix)**
```al
tableextension 50100 "Customer Extension PTE" extends Customer
{
    fields
    {
        field(50100; "PTE Loyalty Points"; Integer) { }  // ❌ PTE as prefix, not suffix
    }
    
    procedure PTECalculateDiscount(): Decimal  // ❌ PTE as prefix, not suffix
    begin
        // Logic here
    end;
}
```

## Why This Rule Exists (iFacto Perspective)

### Clear Identification
- **Instant visibility:** Developers immediately know which fields/procedures are custom PTE additions
- **Code clarity:** When reading AL code, PTE suffix makes customizations obvious at a glance
- **Review efficiency:** Code reviewers can quickly identify and focus on custom extensions

### Upgrade Safety
- **Conflict prevention:** Reduces risk of naming conflicts with future BC base app updates
- **Future-proofing:** If Microsoft adds a field with the same name, the PTE suffix prevents collision
- **Safer migrations:** Upgrades are less likely to break due to naming conflicts

### Maintenance & Collaboration
- **Team awareness:** All developers instantly recognize customizations vs standard BC fields
- **Documentation:** Self-documenting code - PTE suffix indicates "this is our custom extension"
- **Troubleshooting:** When debugging, PTE suffix helps identify where custom logic exists

### Standards Compliance
- **Microsoft best practices:** Aligns with Microsoft's recommended PTE naming patterns
- **Industry standard:** Common pattern in BC partner community for extension identification
- **Consistency:** Enforces uniform approach across all iFacto BC projects

## Enforcement

### AI Specialist Responsibility

**bc-code-intel specialists (especially `waldo-company`) must:**
1. **Validate ALL TableExtension objects** for PTE suffix compliance
2. **Check both fields AND procedures** - commonly missed items
3. **Verify capitalization** - "PTE" not "pte" or "Pte"
4. **Confirm suffix positioning** - must be at the END, not beginning
5. **Reject code without PTE** - This is a MANDATORY rule with no exceptions

### Common Violations to Watch For
- New fields in TableExtension missing PTE suffix
- New procedures in TableExtension missing PTE suffix
- Wrong capitalization ("pte", "Pte" instead of "PTE")
- PTE used as prefix instead of suffix
- Assuming PageExtension needs PTE (it doesn't - only TableExtension)

### Transition Period Status

> **⚠️ CURRENT STATUS:** This rule is currently reporting as **warnings** to allow adaptation time.  
> **THESE WILL BECOME ERRORS:** Expected upgrade to error severity in **February 2026**.

During the transition period:
- Code with missing PTE suffix will trigger **warnings**
- Developers should fix violations proactively
- New code must comply immediately (don't add technical debt)

After transition (February 2026+):
- Missing PTE suffix will be **compilation errors**
- Code will not build without compliance
- Zero tolerance policy

## Related Guidelines

- [iFacto Naming Conventions](./ifacto-naming-conventions.md) - General naming standards
- [iFacto Single Object Per File](./ifacto-single-object-per-file.md) - File organization rules

## Quick Reference

| Scenario | PTE Required? | Example |
|----------|---------------|---------|
| TableExtension field | ✅ YES | `field(50100; "Custom Field PTE"; Text[50])` |
| TableExtension procedure | ✅ YES | `procedure CustomLogicPTE()` |
| Base Table field | ❌ NO | `field(1; "No."; Code[20])` |
| Base Table procedure | ❌ NO | `procedure ValidateCustomer()` |
| PageExtension field | ❌ NO | `field("Custom Field"; Rec."Custom Field PTE")` |
| Codeunit procedure | ❌ NO | `procedure ProcessData()` |

---

**Remember:** PTE suffix is MANDATORY for TableExtension objects only. When in doubt, add PTE - it's better to have it when not strictly needed than to miss it where required.
