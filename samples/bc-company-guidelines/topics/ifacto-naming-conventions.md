---
id: "company/naming-conventions"
title: "iFacto Naming Conventions for Business Central"
domain: "company-standards"
tags: ["naming", "conventions", "standards", "code-quality", "mandatory", "ifacto"]
difficulty: "beginner"
bc_version: "14+"
estimated_time: "15 minutes"
priority: "high"
status: "mandatory"
added: "October 2025"
---

# iFacto Naming Conventions for Business Central

**Company Standard - High Priority**

## What Makes This Company-Specific

Standard BC development recommends PascalCase and meaningful names, but **iFacto enforces** stricter rules: **ALL identifiers AND captions MUST be in English** (no Dutch in code or Caption properties), mandatory "Temp" prefix for temporary records, and strict variable declaration ordering. These are company-wide mandates with zero exceptions.

## Overview

All Business Central AL code must follow these naming conventions to ensure consistency, maintainability, and team collaboration.

## Core Rules

### 1. PascalCase for All Identifiers

**All variables, objects, and symbols must use PascalCase:**

```al
// ✅ CORRECT
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    TotalAmount: Decimal;
    ProcessingComplete: Boolean;

// ❌ WRONG
var
    customer: Record Customer;          // lowercase
    sales_header: Record "Sales Header"; // snake_case
    total_amount: Decimal;               // snake_case
```

### 2. Temporary Variable Prefix

**Always prefix temporary records with "Temp":**

```al
// ✅ CORRECT
var
    TempSalesLine: Record "Sales Line" temporary;
    TempCustomer: Record Customer temporary;
    TempItemBuffer: Record "Item" temporary;

// ❌ WRONG
var
    SalesLine: Record "Sales Line" temporary;  // Missing Temp prefix
    TempSL: Record "Sales Line" temporary;     // Abbreviation unclear
```

### 3. English-Only Identifiers AND Captions

**CRITICAL: All technical identifiers AND Captions MUST be in English.**

This applies to:
- Object names (tables, pages, codeunits, reports, etc.)
- Field names (identifiers)
- **Caption properties** ← MUST be English too!
- Procedure/function names
- Variable names
- Parameter names
- Enum names and values
- Interface names

**Translation Rule:**
- ❌ Do NOT use Caption property for non-English translations
- ✅ Use Business Central translation files (.xlf) for all localization
- ✅ Keep ALL code and captions in English

```al
// ✅ CORRECT - English identifiers AND English captions
table 50100 "Customer Information"
{
    Caption = 'Customer Information';  // ✅ English caption
    
    field(1; "Customer No."; Code[20])
    {
        Caption = 'Customer No.';  // ✅ English caption
    }
    field(2; Description; Text[100])
    {
        Caption = 'Description';  // ✅ English caption
    }
}

procedure CalculateTotal()
var
    TotalAmount: Decimal;
begin
end;

// ❌ WRONG - Dutch identifiers
table 50100 "Klant Informatie"  // BAD - Dutch identifier
{
    field(1; "Klant Nr."; Code[20]) { }  // BAD - Dutch identifier
    field(2; Omschrijving; Text[100]) { }  // BAD - Dutch identifier
}

// ❌ WRONG - Dutch captions (also not allowed!)
table 50100 "Customer Information"  // ✅ English identifier is correct
{
    Caption = 'Klant Informatie';  // ❌ BAD - Dutch caption NOT allowed
    
    field(1; "Customer No."; Code[20])
    {
        Caption = 'Klant Nr.';  // ❌ BAD - Dutch caption NOT allowed
    }
    field(2; Description; Text[100])
    {
        Caption = 'Omschrijving';  // ❌ BAD - Dutch caption NOT allowed
    }
}

procedure BerekenTotaal()  // ❌ BAD - Dutch identifier
var
    TotaalBedrag: Decimal;  // ❌ BAD - Dutch identifier
```

**How to Provide Translations:**

Instead of using Caption for translations, use Business Central's translation system:

1. Keep all code in English (identifiers AND captions)
2. Generate translation file (.xlf) from BC
3. Add translations in the .xlf file
4. BC will automatically use translations at runtime based on user language

```al
// ✅ CORRECT Approach
table 50100 "Customer Information"
{
    Caption = 'Customer Information';  // English in code
    // Dutch "Klant Informatie" goes in .xlf translation file
    
    field(1; "Customer No."; Code[20])
    {
        Caption = 'Customer No.';  // English in code
        // Dutch "Klant Nr." goes in .xlf translation file
    }
}
```

**Translation Reference for Common Terms:**

| Dutch (WRONG) | English (CORRECT) |
|---------------|-------------------|
| Klant | Customer |
| Omschrijving | Description |
| Bedrag | Amount |
| Totaal | Total |
| Datum | Date |
| Nummer | Number |
| Berekenen | Calculate |
| Verwerken | Process |

### 4. Variable Declaration Order

Variables must be declared in this specific order:

1. Record
2. Report
3. Codeunit
4. XmlPort
5. Page
6. Query
7. Notification
8. BigText
9. DateFormula
10. RecordId
11. RecordRef
12. FieldRef
13. FilterPageBuilder
14. Other types (Text, Integer, Decimal, Boolean, Date, etc.)

```al
// ✅ CORRECT - Proper ordering
var
    Customer: Record Customer;
    SalesHeader: Record "Sales Header";
    TempSalesLine: Record "Sales Line" temporary;
    ProcessingReport: Report "Processing Report";
    HelperCodeunit: Codeunit "Helper Functions";
    CustomerName: Text[100];
    TotalAmount: Decimal;
    ProcessingComplete: Boolean;

// ❌ WRONG - Incorrect ordering
var
    TotalAmount: Decimal;              // Should be after codeunits
    Customer: Record Customer;
    ProcessingComplete: Boolean;       // Should be after Decimal
    SalesHeader: Record "Sales Header";
```

## Label Variables

### Label Variable Naming

Label variables follow a specific naming pattern based on usage:

```al
var
    // Error messages: <Context>Err
    CustomerNotFoundErr: Label 'Customer %1 does not exist.';
    InvalidQuantityErr: Label 'Quantity must be greater than zero.';
    
    // Questions/Confirmations: <Context>Qst
    DeleteConfirmQst: Label 'Are you sure you want to delete customer %1?';
    PostOrderQst: Label 'Post sales order %1?';
    
    // Information messages: <Context>Msg
    CustomerCreatedMsg: Label 'Customer %1 has been created successfully.';
    ProcessCompleteMsg: Label 'Process completed. %1 records processed.';
    
    // Status labels: <Context>Lbl
    ProcessingLbl: Label 'Processing...';
    CompletedLbl: Label 'Completed';
```

## Object Naming

### File Naming Convention

Each .al file must contain exactly ONE object and follow this pattern:

**Pattern:** `<ObjectType> <ID> <PascalCaseName><Suffix>.al`

```
✅ CORRECT Examples:
- Table 50100 Customer Extension.al
- TableExt 50101 Customer Ext.al
- Page 50102 Customer Card Extension.al
- PageExt 50103 Customer Card Ext.al
- Codeunit 50104 Customer Management.al
- Codeunit 50105 Customer Management Tests.al

❌ WRONG Examples:
- customer_extension.al           (wrong case, missing object type/ID)
- 50100.al                        (missing object type and name)
- CustomerExtension.al            (missing object type and ID)
- CustomerTablesAndPages.al       (implies multiple objects)
```

### Object Property Names

Use descriptive PascalCase names for all properties:

```al
table 50100 "Customer Information"
{
    Caption = 'Customer Information';
    DataClassification = CustomerContent;
    
    fields
    {
        field(1; "Customer No."; Code[20])
        {
            Caption = 'Customer No.';
            NotBlank = true;
        }
        field(2; "Full Name"; Text[100])
        {
            Caption = 'Full Name';
        }
    }
}
```

## Procedure Naming

### Public Procedures

Use descriptive verb-noun patterns:

```al
// ✅ CORRECT - Clear action and object
procedure CreateCustomer(CustomerNo: Code[20])
procedure CalculateTotalAmount(): Decimal
procedure ValidateShipmentDate(ShipmentDate: Date)
procedure UpdateCustomerBalance(CustomerNo: Code[20])

// ❌ WRONG - Unclear or inconsistent
procedure DoStuff()                    // Too vague
procedure customer_create()            // Wrong case
procedure Calc()                       // Too abbreviated
```

### Local Procedures

Follow same rules as public procedures, but consider using more specific names:

```al
local procedure CalculateLineDiscount(var SalesLine: Record "Sales Line")
local procedure ValidateCustomerCreditLimit(CustomerNo: Code[20])
local procedure UpdateInternalBuffer()
```

## Common Patterns

### Error Variable Names
```al
var
    CustomerNotFoundErr: Label 'Customer %1 does not exist.';
    InvalidDateErr: Label 'Date cannot be in the past.';
    InsufficientInventoryErr: Label 'Insufficient inventory for item %1.';
    CustomerBlockedErr: Label 'Customer %1 is blocked.';
```

### Confirmation Variable Names
```al
var
    DeleteConfirmQst: Label 'Delete customer %1?';
    PostConfirmQst: Label 'Post sales order %1?';
    OverwriteConfirmQst: Label 'Record already exists. Overwrite?';
```

### Message Variable Names
```al
var
    SuccessMsg: Label 'Operation completed successfully.';
    RecordsProcessedMsg: Label '%1 records have been processed.';
    CustomerCreatedMsg: Label 'Customer %1 created.';
```

## Enforcement

These naming conventions are **mandatory** for all iFacto BC development. AI specialists will:

- ✅ Flag non-English identifiers
- ✅ Flag non-English captions
- ✅ Reject improper variable naming
- ✅ Enforce label variable patterns
- ✅ Require PascalCase consistency
- ✅ Validate variable declaration order

## Related Guidelines

- See `ifacto-error-handling-standards.md` for label variable usage in errors
- See `ifacto-single-object-per-file.md` for file structure requirements
- See `ifacto-meth-codeunit-pattern.md` for Meth codeunit naming

---

*Last Updated: November 2025*
*Maintained by: iFacto Development Team*
