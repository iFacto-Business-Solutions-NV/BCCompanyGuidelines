---
id: "company/ifacto-upgrade-install-sync"
title: "Upgrade and Install Codeunit Synchronization (Company-Specific)"
domain: "company-standards"
tags: ["upgrade-codeunit", "install-codeunit", "data-migration", "deployment", "mandatory", "ifacto"]
difficulty: "intermediate"
bc_version: "14+"
estimated_time: "15 minutes"
priority: "critical"
status: "mandatory"
added: "December 2025"
---

# iFacto Upgrade and Install Codeunit Synchronization (Company-Specific)

**Company Standard - CRITICAL PRIORITY**  
**Status:** MANDATORY for all upgrade scenarios  
**Added:** December 2025  

## What Makes This Company-Specific

This is an **iFacto architectural rule** that enforces data consistency across installation and upgrade scenarios. While standard BC allows separate upgrade logic, **iFacto mandates** that upgrade logic MUST also be called from install codeunits to ensure data integrity in all deployment scenarios.

## The Rule

**When writing upgrade logic in an upgrade codeunit, the same logic MUST also be callable from the corresponding install codeunit. The actual implementation resides in the upgrade codeunit, and the install codeunit calls it.**

### Why This Matters

Without this rule, you can encounter scenarios where:
- Fresh installations skip critical data initialization that upgrades perform
- Data structures are inconsistent between fresh installs and upgraded environments
- Testing scenarios miss critical path differences
- Customers who receive "fresh" installs have different data states than those who upgraded

## The Pattern

### ✅ CORRECT - Install Calls Upgrade Logic

**Upgrade Codeunit:**
```al
codeunit 50100 "My Extension Upgrade"
{
    Subtype = Upgrade;

    trigger OnUpgradePerCompany()
    begin
        InitializeCustomerCategories();
    end;

    // Public procedure so install codeunit can call it
    procedure InitializeCustomerCategories()
    var
        Customer: Record Customer;
        CustomerCategory: Record "Customer Category";
    begin
        if UpgradeTag.HasUpgradeTag(InitializeCustomerCategoriesLbl) then
            exit;

        // Actual implementation - executed in BOTH scenarios
        if Customer.FindSet() then
            repeat
                if CustomerCategory.Get(Customer."Customer Category Code") then begin
                    Customer."Category Priority" := CustomerCategory.Priority;
                    Customer.Modify();
                end;
            until Customer.Next() = 0;

        UpgradeTag.SetUpgradeTag(InitializeCustomerCategoriesLbl);            
    end;

    [EventSubscriber(ObjectType::Codeunit, Codeunit::"Upgrade Tag", 'OnGetPerCompanyUpgradeTags', '', false, false)]
    local procedure OnGetPerCompanyUpgradeTags(var PerCompanyUpgradeTags: List of [Code[250]]);
    begin
        PerCompanyUpgradeTags.Add(InitializeCustomerCategoriesLbl);
    end;

    var
        UpgradeTag: Codeunit "Upgrade Tag";
        InitializeCustomerCategoriesLbl: Label 'iFacto-InitializeCustomerCategorie-20251222', Locked = true;    
}
```

**Install Codeunit:**
```al
codeunit 50101 "My Extension Install"
{
    Subtype = Install;

    trigger OnInstallAppPerCompany()
    var
        UpgradeLogic: Codeunit "My Extension Upgrade";
    begin
        // Call the shared logic from upgrade codeunit
        UpgradeLogic.InitializeCustomerCategories();
    end;
}
```

### ❌ WRONG - Upgrade Logic Not Called from Install

**This violates iFacto standards:**
```al
codeunit 50100 "My Extension Upgrade"
{
    Subtype = Upgrade;

    trigger OnUpgradePerCompany()
    begin
        InitializeCustomerCategories();
    end;

    local procedure InitializeCustomerCategories()
    var
        Customer: Record Customer;
        CustomerCategory: Record "Customer Category";
    begin
        // ❌ WRONG: This is LOCAL - install codeunit cannot call it!
        // Fresh installations will NOT have this data initialized!
        if Customer.FindSet() then
            repeat
                if CustomerCategory.Get(Customer."Customer Category Code") then begin
                    Customer."Category Priority" := CustomerCategory.Priority;
                    Customer.Modify();
                end;
            until Customer.Next() = 0;
    end;
}

codeunit 50101 "My Extension Install"
{
    Subtype = Install;

    trigger OnInstallAppPerCompany()
    begin
        // ❌ WRONG: Cannot call upgrade logic because it's local
        // Fresh installs skip this critical logic!
    end;
}
```

## Implementation Guidelines

### 1. Place Shared Logic in Upgrade Codeunit

The upgrade codeunit should contain the actual implementation as a **public procedure** that can be called from:
- The upgrade trigger (for upgrades)
- The install codeunit (for fresh installations)

### 2. Install Codeunit Calls Upgrade Logic

The install codeunit should primarily act as a coordinator, calling the shared procedures from the upgrade codeunit.

```al
// ✅ CORRECT Pattern
codeunit 50100 "Extension Upgrade"
{
    Subtype = Upgrade;

    trigger OnUpgradePerCompany()
    begin
        // Run shared logic directly
        InitializeDefaultSettings();
        MigrateHistoricalData();
        SetupRequiredTables();
    end;

    procedure InitializeDefaultSettings()
    begin
        // Implementation here
    end;

    procedure MigrateHistoricalData()
    begin
        // Implementation here
    end;

    procedure SetupRequiredTables()
    begin
        // Implementation here
    end;
}

codeunit 50101 "Extension Install"
{
    Subtype = Install;

    trigger OnInstallAppPerCompany()
    var
        UpgradeLogic: Codeunit "Extension Upgrade";
    begin
        // Same logic runs on fresh install by calling upgrade codeunit
        UpgradeLogic.InitializeDefaultSettings();
        UpgradeLogic.MigrateHistoricalData();
        UpgradeLogic.SetupRequiredTables();
    end;
}
```

### 3. Create Install Codeunit If Missing

**CRITICAL: If you have an upgrade codeunit but no install codeunit, you MUST create one!**

Without an install codeunit, fresh installations will skip all initialization logic, leading to inconsistent data states.

```al
// ✅ ALWAYS create both codeunits together

// Upgrade Codeunit (already exists)
codeunit 50100 "Extension Upgrade"
{
    Subtype = Upgrade;

    trigger OnUpgradePerCompany()
    begin
        InitializeDefaultSettings();
    end;

    procedure InitializeDefaultSettings()
    begin
        // Shared initialization logic
    end;
}

// Install Codeunit (CREATE THIS if it doesn't exist!)
codeunit 50101 "Extension Install"
{
    Subtype = Install;

    trigger OnInstallAppPerCompany()
    var
        UpgradeLogic: Codeunit "Extension Upgrade";
    begin
        // Call all shared procedures from upgrade codeunit
        UpgradeLogic.InitializeDefaultSettings();
    end;
}
```

**When reviewing existing code:**
- ✅ If you find upgrade codeunit with public procedures → Check if install codeunit exists
- ❌ If install codeunit is missing → CREATE IT immediately
- ✅ Ensure install codeunit calls ALL shared public procedures from upgrade codeunit

**Why this is critical:**
- Fresh installations are common (new customers, new environments, testing)
- Without install codeunit, fresh installs lack critical data initialization
- Creates hidden bugs that only appear in production for new customers
- Testing in upgraded environments won't catch these issues

### 4. Handle Upgrade-Specific Scenarios Separately

If you need upgrade-only logic (e.g., data transformations from old schema), keep that separate and clearly documented:

```al
codeunit 50100 "Extension Upgrade"
{
    Subtype = Upgrade;

    trigger OnUpgradePerCompany()
    begin
        // Step 1: Upgrade-specific transformations
        MigrateOldSchemaToNewSchema();
        
        // Step 2: Run shared initialization logic
        InitializeDefaultSettings();
    end;

    local procedure MigrateOldSchemaToNewSchema()
    begin
        // ✅ This is upgrade-only and doesn't need to be called from install
        // Document WHY this is upgrade-only
    end;

    procedure InitializeDefaultSettings()
    begin
        // ✅ This is PUBLIC so install codeunit can call it
        // Implementation here
    end;
}

codeunit 50101 "Extension Install"
{
    Subtype = Install;

    trigger OnInstallAppPerCompany()
    var
        UpgradeLogic: Codeunit "Extension Upgrade";
    begin
        // Call shared logic from upgrade codeunit
        UpgradeLogic.InitializeDefaultSettings();
    end;
}
```

## Why This Matters (iFacto Perspective)

At iFacto, we've encountered production issues where:
1. **Fresh customer installations** lacked critical data that upgraded customers had
2. **Test environments** (clean installs) behaved differently from production (upgraded)
3. **Rollback scenarios** failed because install logic was incomplete
4. **Support burden** increased due to inconsistent data states across customers

By enforcing this rule, we ensure:
- ✅ Consistent data state across all installation methods
- ✅ Upgrade logic is tested in fresh install scenarios too
- ✅ Reduced support incidents from missing data initialization
- ✅ Clear separation of concerns (shared vs upgrade-only logic)
- ✅ Single source of truth for data initialization logic in upgrade codeunit

## Enforcement

### BC Code Review Checklist

When reviewing code with upgrade codeunits:
- [ ] **Install codeunit EXISTS** - Create one if missing!
- [ ] Every upgrade procedure that initializes data is PUBLIC so install codeunit can call it
- [ ] Upgrade codeunit contains all shared initialization logic as public procedures
- [ ] Install codeunit calls upgrade codeunit procedures for data initialization
- [ ] No duplicate logic between install and upgrade codeunits
- [ ] Comments explain any upgrade-only transformations (local procedures)
- [ ] Fresh install testing performed to verify install codeunit works correctly

### AI Specialist Guidance

AI specialists should:
1. **CRITICAL: Check if install codeunit exists** - If upgrade codeunit exists but install doesn't, flag as critical violation and provide install codeunit template
2. **Immediately flag** any upgrade procedures that aren't callable from install codeunit (must be public, not local)
3. **Suggest refactoring** duplicate logic into shared public procedures in upgrade codeunit
4. **Request documentation** for any local upgrade procedures justifying why they're upgrade-only
5. **Validate** that install codeunit calls upgrade codeunit for all data initialization
6. **Verify** that fresh installations and upgrades result in equivalent data states
7. **Recommend fresh install testing** to ensure install codeunit works correctly

## Common Scenarios

### Scenario 1: Adding New Default Data

```al
// ✅ CORRECT
codeunit "Extension Upgrade"
{
    trigger OnUpgradePerCompany()
    begin
        AddDefaultPaymentTerms();
    end;

    procedure AddDefaultPaymentTerms()
    var
        PaymentTerms: Record "Payment Terms";
    begin
        if not PaymentTerms.Get('NET30') then begin
            PaymentTerms.Init();
            PaymentTerms.Code := 'NET30';
            PaymentTerms.Description := 'Net 30 Days';
            PaymentTerms."Due Date Calculation" := '<30D>';
            PaymentTerms.Insert(true);
        end;
    end;
}

// Install calls the same logic
codeunit "Extension Install"
{
    trigger OnInstallAppPerCompany()
    var
        UpgradeLogic: Codeunit "Extension Upgrade";
    begin
        UpgradeLogic.AddDefaultPaymentTerms();
    end;
}
```

### Scenario 2: Field Data Migration

```al
// ✅ CORRECT
codeunit "Extension Upgrade"
{
    trigger OnUpgradePerCompany()
    begin
        InitializeCustomerRiskLevels();
    end;

    procedure InitializeCustomerRiskLevels()
    var
        Customer: Record Customer;
    begin
        if Customer.FindSet(true) then
            repeat
                if Customer."Risk Level" = Customer."Risk Level"::" " then begin
                    Customer."Risk Level" := Customer."Risk Level"::Medium;
                    Customer.Modify();
                end;
            until Customer.Next() = 0;
    end;
}

// Install calls the same logic
codeunit "Extension Install"
{
    trigger OnInstallAppPerCompany()
    var
        UpgradeLogic: Codeunit "Extension Upgrade";
    begin
        UpgradeLogic.InitializeCustomerRiskLevels();
    end;
}
```

## Related Guidelines

- [Meth Codeunit Pattern](ifacto-meth-codeunit-pattern.md) - Business logic separation patterns
- [Error Handling Standards](ifacto-error-handling-standards.md) - Error handling in install/upgrade codeunits
- [Documentation Standards](ifacto-documentation-standards.md) - Documenting upgrade and installation procedures

## References

- [Microsoft Learn: Upgrade Codeunits](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-upgrading-extensions)
- [Microsoft Learn: Install Codeunits](https://learn.microsoft.com/en-us/dynamics365/business-central/dev-itpro/developer/devenv-oninstall-codeunit)

---

**Summary:** At iFacto, upgrade logic MUST be callable from install codeunits to ensure consistent data states across all deployment scenarios. Place shared logic in upgrade codeunits as public procedures, and have install codeunits call those procedures. This prevents situations where fresh installations lack critical data initialization that upgrades perform.
