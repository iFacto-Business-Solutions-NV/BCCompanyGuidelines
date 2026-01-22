---
id: "company/ifacto-error-handling-standards"
title: "iFacto Error Handling Standards"
domain: "company-standards"
tags: ["error-handling", "labels", "localization", "user-messages", "mandatory", "ifacto"]
difficulty: "beginner"
bc_version: "14+"
estimated_time: "15 minutes"
priority: "high"
status: "mandatory"
added: "October 2025"
---

# iFacto Error Handling Standards

**Company Standard - High Priority**

## Critical Rule: Label Variables for ALL User-Facing Text

**MANDATORY:** All user-facing text (errors, messages, confirmations) MUST use label variables. Never use hardcoded text strings.

## Error Messages

### Basic Error Pattern

```al
// ✅ CORRECT - Label variable with clear naming
procedure ValidateCustomer(CustomerNo: Code[20])
var
    Customer: Record Customer;
    CustomerNotFoundErr: Label 'Customer %1 does not exist.', Comment = '%1 = Customer Number';
begin
    if not Customer.Get(CustomerNo) then
        Error(CustomerNotFoundErr, CustomerNo);
end;

// ❌ WRONG - Hardcoded text
procedure ValidateCustomerWrong(CustomerNo: Code[20])
var
    Customer: Record Customer;
begin
    if not Customer.Get(CustomerNo) then
        Error('Customer %1 does not exist.', CustomerNo);  // BAD - hardcoded
end;
```

### Error Variable Naming Convention

**Pattern:** `<Context><Reason>Err`

```al
var
    CustomerNotFoundErr: Label 'Customer %1 does not exist.';
    ItemNotFoundErr: Label 'Item %1 does not exist.';
    CustomerBlockedErr: Label 'Customer %1 is blocked for %2.';
    InvalidQuantityErr: Label 'Quantity must be greater than zero.';
    InvalidDateRangeErr: Label 'End date cannot be earlier than start date.';
    InsufficientCreditErr: Label 'Customer %1 has insufficient credit.';
```

### Complex Error Messages with Multiple Parameters

```al
var
    InsufficientInventoryErr: Label 'Insufficient inventory for item %1. Available: %2, Required: %3, Location: %4.', 
        Comment = '%1 = Item Number, %2 = Available Quantity, %3 = Required Quantity, %4 = Location Code';

procedure ValidateInventory(ItemNo: Code[20]; RequiredQty: Decimal; LocationCode: Code[10])
var
    Item: Record Item;
    AvailableQty: Decimal;
begin
    // Calculate available quantity
    AvailableQty := CalculateAvailableInventory(ItemNo, LocationCode);
    
    if AvailableQty < RequiredQty then
        Error(InsufficientInventoryErr, ItemNo, AvailableQty, RequiredQty, LocationCode);
end;
```

## Confirmation Dialogs

### Basic Confirmation Pattern

```al
// ✅ CORRECT - Label variable for confirmation
procedure DeleteCustomerWithConfirmation(var Customer: Record Customer)
var
    DeleteCustomerQst: Label 'Are you sure you want to delete customer %1?', Comment = '%1 = Customer Number';
begin
    if not Confirm(DeleteCustomerQst, false, Customer."No.") then
        exit;
        
    Customer.Delete(true);
end;

// ❌ WRONG - Hardcoded confirmation text
procedure DeleteCustomerWrong(var Customer: Record Customer)
begin
    if not Confirm('Delete customer %1?', false, Customer."No.") then  // BAD
        exit;
        
    Customer.Delete(true);
end;
```

### Confirmation Variable Naming Convention

**Pattern:** `<Context><Action>Qst`

```al
var
    DeleteCustomerQst: Label 'Are you sure you want to delete customer %1?';
    PostOrderQst: Label 'Post sales order %1?';
    OverwriteQst: Label 'Record already exists. Overwrite?';
    CancelProcessQst: Label 'Cancel the current process?';
    RelatedDataQst: Label 'Customer %1 has related transactions. Delete anyway?';
```

### Complex Confirmation with Context

```al
procedure DeleteCustomerWithValidation(var Customer: Record Customer)
var
    DeleteCustomerQst: Label 'Are you sure you want to delete customer %1?', Comment = '%1 = Customer Number';
    RelatedDataQst: Label 'Customer %1 has %2 related transactions. Delete anyway?', 
        Comment = '%1 = Customer Number, %2 = Transaction Count';
    TransactionCount: Integer;
begin
    if not Confirm(DeleteCustomerQst, false, Customer."No.") then
        exit;
        
    TransactionCount := CountCustomerTransactions(Customer."No.");
    if TransactionCount > 0 then
        if not Confirm(RelatedDataQst, false, Customer."No.", TransactionCount) then
            exit;
            
    Customer.Delete(true);
end;
```

## Information Messages

### Basic Message Pattern

```al
// ✅ CORRECT - Label variable for messages
procedure ProcessSalesOrder(var SalesHeader: Record "Sales Header")
var
    OrderProcessedMsg: Label 'Sales order %1 has been processed successfully.', Comment = '%1 = Sales Order Number';
    LinesProcessedMsg: Label 'Processed %1 lines for order %2.', Comment = '%1 = Number of Lines, %2 = Order Number';
    ProcessedLines: Integer;
begin
    // Process order logic
    ProcessedLines := ProcessOrderLines(SalesHeader);
    
    Message(OrderProcessedMsg, SalesHeader."No.");
    Message(LinesProcessedMsg, ProcessedLines, SalesHeader."No.");
end;

// ❌ WRONG - Hardcoded message text
procedure ProcessSalesOrderWrong(var SalesHeader: Record "Sales Header")
begin
    Message('Sales order %1 has been processed.', SalesHeader."No.");  // BAD
end;
```

### Message Variable Naming Convention

**Pattern:** `<Context><Purpose>Msg`

```al
var
    CustomerCreatedMsg: Label 'Customer %1 has been created successfully.';
    OrderPostedMsg: Label 'Sales order %1 posted successfully.';
    ProcessCompleteMsg: Label 'Process completed. %1 records processed.';
    UpdateSuccessMsg: Label 'Customer %1 updated successfully.';
    NoRecordsMsg: Label 'No records found matching the criteria.';
```

## Label Variable Organization

### Group by Functional Area

```al
codeunit 50100 "Sales Order Management"
{
    // [Error Messages]
    var
        CustomerNotFoundErr: Label 'Customer %1 does not exist.';
        ItemNotFoundErr: Label 'Item %1 does not exist.';
        InsufficientInventoryErr: Label 'Insufficient inventory for item %1.';
        InvalidQuantityErr: Label 'Quantity must be greater than zero.';

    // [Confirmation Messages]
    var
        DeleteOrderQst: Label 'Delete sales order %1?';
        PostOrderQst: Label 'Post sales order %1?';
        CancelOrderQst: Label 'Cancel sales order %1?';

    // [Information Messages]
    var
        OrderCreatedMsg: Label 'Sales order %1 created successfully.';
        OrderPostedMsg: Label 'Sales order %1 posted successfully.';
        ProcessCompleteMsg: Label 'Process completed.';

    // [Status Labels]
    var
        ProcessingLbl: Label 'Processing...';
        CompletedLbl: Label 'Completed';
        PendingLbl: Label 'Pending';
}
```

## Comment Attributes for Placeholders

### Always Document Placeholders

```al
// ✅ GOOD - Clear comments for translators
var
    ComplexErrorMsg: Label 'Calculation failed for %1 %2. Expected %3, got %4.', 
        Comment = '%1 = Document Type, %2 = Document Number, %3 = Expected Amount, %4 = Actual Amount';
    
    ValidationResultMsg: Label 'Validation completed: %1 passed, %2 failed, %3 warnings.', 
        Comment = '%1 = Passed Count, %2 = Failed Count, %3 = Warning Count';

// ❌ AVOID - No comments for placeholders
var
    ComplexErrorMsg: Label 'Calculation failed for %1 %2. Expected %3, got %4.';  // Missing comments
```

### Simple Placeholders (Optional Comments)

```al
// Comments optional for obvious single placeholders
var
    CustomerNotFoundErr: Label 'Customer %1 does not exist.', Comment = '%1 = Customer Number';
    ItemUpdatedMsg: Label 'Item %1 updated.', Comment = '%1 = Item Number';
    
// But always include for clarity
var
    ProcessingMsg: Label 'Processing %1...', Comment = '%1 = Entity being processed';
```

## Localization Considerations

### Locked Attribute for Technical Text

```al
// ✅ CORRECT - Lock technical identifiers
var
    TechnicalErrorMsg: Label 'Error in function ProcessData: %1', Locked = true;
    APIEndpointLbl: Label 'https://api.example.com/v1/data', Locked = true;
    FilePathLbl: Label 'C:\ProgramData\AppFolder\Config.json', Locked = true;

// ✅ CORRECT - Don't lock user-facing messages
var
    UserFriendlyMsg: Label 'Your data has been saved successfully.';
    ValidationErrorMsg: Label 'Please check the following fields: %1';
```

### Avoid Text Concatenation

```al
// ❌ WRONG - Hard to translate
var
    CustomerNo: Code[20];
begin
    Message('Customer ' + CustomerNo + ' has been created.');
end;

// ✅ CORRECT - Translatable format
var
    CustomerNo: Code[20];
    CustomerCreatedMsg: Label 'Customer %1 has been created.', Comment = '%1 = Customer Number';
begin
    Message(CustomerCreatedMsg, CustomerNo);
end;
```

## Common Anti-Patterns to Avoid

### Never Do This

```al
// ❌ BAD: Hardcoded error text
Error('Customer does not exist');

// ❌ BAD: Hardcoded confirmation text
Confirm('Are you sure?');

// ❌ BAD: Hardcoded message text
Message('Process completed');

// ❌ BAD: Text concatenation
Message('Customer ' + CustomerNo + ' created');

// ❌ BAD: Mixed hardcoded and variables
Error('Customer ' + CustomerNo + ' does not exist');

// ❌ BAD: StrSubstNo with hardcoded text
ErrorMessage := StrSubstNo('Customer %1 does not exist', CustomerNo);
```

### Always Do This

```al
// ✅ GOOD: Label variables for all text
var
    CustomerNotFoundErr: Label 'Customer %1 does not exist.', Comment = '%1 = Customer Number';
    ConfirmDeleteQst: Label 'Are you sure you want to delete this record?';
    ProcessCompletedMsg: Label 'Process completed successfully.';
    CustomerCreatedMsg: Label 'Customer %1 has been created.', Comment = '%1 = Customer Number';

// Usage
if not Customer.Get(CustomerNo) then
    Error(CustomerNotFoundErr, CustomerNo);
    
if Confirm(ConfirmDeleteQst) then
    DeleteRecord();
    
Message(ProcessCompletedMsg);
Message(CustomerCreatedMsg, CustomerNo);
```

## Error Handling Best Practices

### Provide Context in Errors

```al
// ✅ GOOD - Specific error with context
var
    CustomerCreditLimitErr: Label 'Customer %1 has insufficient credit. Available: %2, Required: %3.', 
        Comment = '%1 = Customer Number, %2 = Available Credit, %3 = Required Credit';

// ❌ AVOID - Generic error without context
var
    CreditLimitErr: Label 'Insufficient credit.';
```

### Business Logic Validation

```al
var
    InvalidDateRangeErr: Label 'End date %1 cannot be earlier than start date %2.', 
        Comment = '%1 = End Date, %2 = Start Date';
    RequiredFieldErr: Label '%1 must be specified.', Comment = '%1 = Field Caption';
    DuplicateRecordErr: Label 'A record with %1 %2 already exists.', 
        Comment = '%1 = Field Name, %2 = Field Value';

procedure ValidateDateRange(StartDate: Date; EndDate: Date)
begin
    if EndDate < StartDate then
        Error(InvalidDateRangeErr, EndDate, StartDate);
end;
```

## Enforcement

These error handling standards are **mandatory** for all iFacto BC development. AI specialists will:

- ✅ Reject hardcoded error/message/confirmation text
- ✅ Require label variables for all user-facing text
- ✅ Enforce proper naming conventions for label variables
- ✅ Require Comment attributes for placeholders
- ✅ Flag text concatenation patterns

## Related Guidelines

- [Naming Conventions](ifacto-naming-conventions.md) for label variable naming patterns
- [Documentation Standards](ifacto-documentation-standards.md) for user-facing text documentation
- [Single Object Per File](ifacto-single-object-per-file.md) for file organization
