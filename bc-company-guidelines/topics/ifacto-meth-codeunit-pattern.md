---
id: "company/ifacto-meth-codeunit-pattern"
title: "iFacto Meth Codeunit Pattern (Company-Specific)"
domain: "company-standards"
tags: ["meth-codeunit", "architecture", "business-logic", "separation-of-concerns", "mandatory", "ifacto"]
difficulty: "intermediate"
bc_version: "14+"
estimated_time: "20 minutes"
priority: "high"
status: "mandatory"
added: "October 2025"
---

# iFacto Meth Codeunit Pattern (Company-Specific)

**Company Architectural Pattern - HIGH PRIORITY**  
**Status:** MANDATORY for all table business logic  
**Added:** October 2025  

## What Makes This Company-Specific

This is an **iFacto architectural pattern** that goes beyond standard BC practices. While standard BC allows business logic in tables, **iFacto mandates** separation into Meth codeunits with specific restrictions.

## The Pattern

### Core Rule

**When adding ANY function to a table that contains business logic:**
1. Create a corresponding Meth codeunit named `[Business Logic] Meth`
2. Place ALL business logic in the Meth codeunit
3. The table calls the Meth codeunit (table is just the interface)

### Critical Restrictions (iFacto-Specific)

#### 1. ONE Public Procedure Per Meth Codeunit

```al
// ✅ CORRECT - Only ONE non-local procedure
codeunit 50111 "Customer CalculateScore Meth"
{
    // The ONLY public procedure - main entry point
    procedure CalculateScore(CustomerNo: Code[20]) Score: Integer
    var
        IsHandled: Boolean;
    begin
        OnBeforeCalculateScore(CustomerNo, Score, IsHandled);
        DoCalculateScore(CustomerNo, Score, IsHandled);
        OnAfterCalculateScore(CustomerNo, Score);
    end;

    // ALL other procedures MUST be local
    local procedure DoCalculateScore(CustomerNo: Code[20]; var Score: Integer; IsHandled: Boolean)
    var
        Customer: Record Customer;
    begin
        if IsHandled then exit;
        
        Customer.SetLoadFields("Custom Score");
        if not Customer.Get(CustomerNo) then 
            Clear(Customer);
            
        Score := Customer."Custom Score" * 2;
    end;

    local procedure ValidateCustomer(CustomerNo: Code[20]): Boolean
    var
        Customer: Record Customer;
    begin
        exit(Customer.Get(CustomerNo));
    end;

    // Events for extensibility
    [IntegrationEvent(false, false)]
    local procedure OnBeforeCalculateScore(CustomerNo: Code[20]; var Score: Integer; var Handled: Boolean)
    begin
    end;

    [IntegrationEvent(false, false)]
    local procedure OnAfterCalculateScore(CustomerNo: Code[20]; var Score: Integer)
    begin
    end;
}

// ❌ WRONG - Multiple public procedures
codeunit 50111 "Customer CalculateScore Meth"
{
    procedure CalculateScore(CustomerNo: Code[20]) Score: Integer  // Public
    begin
    end;

    procedure ValidateCustomer(CustomerNo: Code[20]): Boolean  // Public - WRONG!
    begin
    end;
}
```

#### 2. Called ONLY from Tables

```al
// ✅ CORRECT - Table calls Meth codeunit
table 50110 "Customer Extension"
{
    procedure CalculateCustomScore(): Integer
    var
        CustomerCalculateScoreMeth: Codeunit "Customer CalculateScore Meth";
    begin
        exit(CustomerCalculateScoreMeth.CalculateScore("Customer No."));
    end;
}

// ❌ WRONG - Page calling Meth codeunit directly
page 50110 "Customer Card Extension"
{
    actions
    {
        action(CalculateScore)
        {
            trigger OnAction()
            var
                CustomerCalculateScoreMeth: Codeunit "Customer CalculateScore Meth";  // FORBIDDEN!
            begin
                CustomerCalculateScoreMeth.CalculateScore("Customer No.");  // DO NOT DO THIS
            end;
        }
    }
}

// ✅ CORRECT - Page calls table function, which calls Meth
page 50110 "Customer Card Extension"
{
    actions
    {
        action(CalculateScore)
        {
            trigger OnAction()
            var
                CustomerExtension: Record "Customer Extension";
            begin
                if CustomerExtension.Get("Customer No.") then
                    CustomerExtension.CalculateCustomScore();  // Table function calls Meth
            end;
        }
    }
}
```

## Complete Example

### Table with Function
```al
table 50110 "Customer Extension"
{
    Caption = 'Customer Extension';
    DataClassification = CustomerContent;
    
    fields
    {
        field(1; "Customer No."; Code[20])
        {
            Caption = 'Customer No.';
            TableRelation = Customer."No.";
        }
        field(2; "Custom Score"; Integer)
        {
            Caption = 'Custom Score';
        }
        field(3; "Rating Level"; Enum "Rating Level")
        {
            Caption = 'Rating Level';
        }
    }
    
    // Business logic delegated to Meth codeunit
    procedure CalculateCustomScore(): Integer
    var
        CustomerCalculateScoreMeth: Codeunit "Customer CalculateScore Meth";
    begin
        exit(CustomerCalculateScoreMeth.CalculateScore("Customer No."));
    end;
    
    procedure DetermineRatingLevel(): Enum "Rating Level"
    var
        CustomerRatingMeth: Codeunit "Customer DetermineRating Meth";
    begin
        exit(CustomerRatingMeth.DetermineRating("Customer No.", "Custom Score"));
    end;
}
```

### Meth Codeunit Structure
```al
codeunit 50111 "Customer CalculateScore Meth"
{
    // ONLY ONE public procedure allowed
    procedure CalculateScore(CustomerNo: Code[20]) Score: Integer
    var
        IsHandled: Boolean;
    begin
        OnBeforeCalculateScore(CustomerNo, Score, IsHandled);
        
        if not IsHandled then
            DoCalculateScore(CustomerNo, Score);
            
        OnAfterCalculateScore(CustomerNo, Score);
    end;

    // All implementation details are local
    local procedure DoCalculateScore(CustomerNo: Code[20]; var Score: Integer)
    var
        Customer: Record Customer;
        SalesHeader: Record "Sales Header";
    begin
        // Complex business logic here
        if not GetCustomer(CustomerNo, Customer) then
            exit;
            
        Score := CalculateBaseScore(Customer);
        Score += CalculateBonusPoints(CustomerNo);
        
        AdjustForSalesHistory(CustomerNo, Score);
    end;

    local procedure GetCustomer(CustomerNo: Code[20]; var Customer: Record Customer): Boolean
    begin
        Customer.SetLoadFields("No.", "Name", "Blocked");
        exit(Customer.Get(CustomerNo));
    end;

    local procedure CalculateBaseScore(Customer: Record Customer): Integer
    begin
        // Base score logic
        exit(100);
    end;

    local procedure CalculateBonusPoints(CustomerNo: Code[20]): Integer
    var
        SalesHeader: Record "Sales Header";
    begin
        // Bonus calculation logic
        SalesHeader.SetRange("Sell-to Customer No.", CustomerNo);
        SalesHeader.SetRange("Document Type", SalesHeader."Document Type"::Order);
        exit(SalesHeader.Count() * 10);
    end;

    local procedure AdjustForSalesHistory(CustomerNo: Code[20]; var Score: Integer)
    begin
        // Historical adjustments
        if Score > 1000 then
            Score := 1000;
    end;

    // Extensibility events
    [IntegrationEvent(false, false)]
    local procedure OnBeforeCalculateScore(CustomerNo: Code[20]; var Score: Integer; var Handled: Boolean)
    begin
    end;

    [IntegrationEvent(false, false)]
    local procedure OnAfterCalculateScore(CustomerNo: Code[20]; var Score: Integer)
    begin
    end;
}
```

## Why This Pattern?

### Benefits

1. **Maintainability:** Business logic isolated and testable
2. **Testability:** Meth codeunits can be unit tested independently
3. **Extensibility:** Events allow customization without modifying code
4. **Separation of Concerns:** Table = data structure, Meth = business logic
5. **Code Reuse:** Logic can be reused through the table's public interface

### iFacto-Specific Restrictions

The **ONE public procedure** and **table-only calling** restrictions are **iFacto additions** that enforce:
- Clear entry points (single public procedure)
- Architectural integrity (only tables call Meth codeunits)
- Easier code review and maintenance
- Prevents misuse of Meth codeunits as general-purpose libraries

## Naming Convention

**Pattern:** `[Table Name] [Function Purpose] Meth`

```al
// Table: Customer Extension
// Meth codeunits:
codeunit 50111 "Customer CalculateScore Meth"
codeunit 50112 "Customer DetermineRating Meth"
codeunit 50113 "Customer ValidateEligibility Meth"

// Table: Sales Order Extension
// Meth codeunits:
codeunit 50114 "Sales Order ApplyDiscount Meth"
codeunit 50115 "Sales Order CalculateTax Meth"
```

## Code Review Checklist

When reviewing code with Meth codeunits:

- [ ] Does the Meth codeunit have **exactly ONE** non-local procedure?
- [ ] Are all helper procedures marked as `local`?
- [ ] Is the Meth codeunit called **only from the corresponding table**?
- [ ] Does the Meth codeunit have OnBefore/OnAfter events for extensibility?
- [ ] Is business logic properly separated from data structure (table)?
- [ ] Are there no direct calls to Meth from pages/reports/other codeunits?

## AI Specialist Enforcement

When specialists (especially @sam-coder, @roger-reviewer, @alex-architect) encounter:

**Adding function to table → Suggest:**
```
"I see you're adding a function to a table. According to iFacto's Meth codeunit 
pattern, you should:
1. Create a Meth codeunit for the business logic
2. Keep only ONE public procedure in the Meth codeunit
3. Have the table function call the Meth codeunit
4. All other procedures in Meth must be local"
```

**Multiple public procedures in Meth → Flag:**
```
"⚠️ This Meth codeunit has multiple public procedures. According to iFacto standards,
Meth codeunits must have exactly ONE public procedure. All helper procedures should
be marked as local."
```

**Non-table calling Meth → Flag:**
```
"⚠️ You're calling a Meth codeunit from a [page/report/codeunit]. According to iFacto
standards, Meth codeunits can ONLY be called from their corresponding table. Please
call the table function instead, which will internally use the Meth codeunit."
```

## Related Guidelines

- [Naming Conventions](ifacto-naming-conventions.md) for Meth codeunit naming patterns
- [Single Object Per File](ifacto-single-object-per-file.md) for file structure requirements
- [Error Handling Standards](ifacto-error-handling-standards.md) for error messages in Meth codeunits

---

**This is an iFacto-specific pattern.** Standard BC development does not require this separation. This pattern was introduced in October 2025 to enforce architectural consistency across all iFacto BC projects.
