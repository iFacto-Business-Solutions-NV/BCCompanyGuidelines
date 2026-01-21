---
id: "company/ifacto-enum-patterns"
title: "iFacto Enum Patterns (Company Delta)"
domain: "company-standards"
tags: ["enum", "extensibility", "interfaces", "mandatory", "ifacto", "patterns"]
difficulty: "intermediate"
bc_version: "14+"
estimated_time: "10 minutes"
priority: "medium"
status: "mandatory"
added: "October 2025"
---

# iFacto Enum Patterns (Company Delta)

**Company Standard - MEDIUM PRIORITY**  
**Status:** MANDATORY for enum creation  
**Added:** October 2025  

## What Makes This Company-Specific

Standard BC guidance says "make enums extensible when needed." **iFacto provides explicit rules** based on interface implementation.

## The Rule: Extensibility Based on Interface Implementation

### When to Make Extensible

**Extensible = true ONLY when enum implements an interface**

```al
// ✅ CORRECT - Extensible because it implements interface
enum 50101 "Payment Method" implements IPaymentProcessor
{
    Extensible = true;  // Correct: has interface implementation

    value(0; " ") { Caption = ' '; }
    value(1; Cash) 
    { 
        Caption = 'Cash';
        Implementation = IPaymentProcessor = CashPaymentProcessor;
    }
    value(2; "Credit Card") 
    { 
        Caption = 'Credit Card';
        Implementation = IPaymentProcessor = CreditCardProcessor;
    }
    value(3; "Bank Transfer") 
    { 
        Caption = 'Bank Transfer';
        Implementation = IPaymentProcessor = BankTransferProcessor;
    }
}
```

### When to Make Non-Extensible

**Extensible = false when enum has NO interface implementation**

```al
// ✅ CORRECT - Non-extensible, no interface
enum 50102 "Document Status"
{
    Extensible = false;  // Correct: no interface implementation

    value(0; " ") { Caption = ' '; }
    value(1; Open) { Caption = 'Open'; }
    value(2; "Pending Approval") { Caption = 'Pending Approval'; }
    value(3; Approved) { Caption = 'Approved'; }
    value(4; Rejected) { Caption = 'Rejected'; }
}
```

## Common Patterns

### Status Enums (Non-Extensible)

```al
// Status enums typically don't need extensibility
enum 50103 "Order Status"
{
    Extensible = false;

    value(0; " ") { Caption = ' '; }
    value(1; New) { Caption = 'New'; }
    value(2; Processing) { Caption = 'Processing'; }
    value(3; Completed) { Caption = 'Completed'; }
    value(4; Cancelled) { Caption = 'Cancelled'; }
}
```

### Type Enums with Interfaces (Extensible)

```al
// Type enums with different implementations = extensible
enum 50104 "Notification Type" implements INotificationSender
{
    Extensible = true;  // Others can add notification types

    value(0; " ") { Caption = ' '; }
    value(1; Email) 
    { 
        Caption = 'Email';
        Implementation = INotificationSender = EmailNotificationSender;
    }
    value(2; SMS) 
    { 
        Caption = 'SMS';
        Implementation = INotificationSender = SMSNotificationSender;
    }
    value(3; Push) 
    { 
        Caption = 'Push Notification';
        Implementation = INotificationSender = PushNotificationSender;
    }
}
```

### Category Enums (Consider Extensibility)

```al
// Categories where others might add values
enum 50105 "Customer Category" implements ICategoryValidator
{
    Extensible = true;  // Partners can add custom categories

    value(0; " ") { Caption = ' '; }
    value(1; Standard) 
    { 
        Caption = 'Standard';
        Implementation = ICategoryValidator = StandardCategoryValidator;
    }
    value(2; Premium) 
    { 
        Caption = 'Premium';
        Implementation = ICategoryValidator = PremiumCategoryValidator;
    }
    value(3; Enterprise) 
    { 
        Caption = 'Enterprise';
        Implementation = ICategoryValidator = EnterpriseCategoryValidator;
    }
}
```

## Decision Matrix

| Scenario | Extensible | Reasoning |
|----------|-----------|-----------|
| Enum implements interface | `true` | Others can add implementations |
| Enum is closed list (status, priority) | `false` | Fixed set of values |
| Enum is configuration option | `false` | Controlled list |
| Enum represents types with behavior | `true` | Different implementations possible |
| Enum without interface | `false` | No extensibility mechanism |

## Anti-Patterns

### ❌ WRONG: Extensible without Interface

```al
// WRONG: Extensible but no interface to implement
enum 50106 "Document Status"
{
    Extensible = true;  // ❌ Wrong: no interface implementation

    value(0; Open) { Caption = 'Open'; }
    value(1; Closed) { Caption = 'Closed'; }
}
```

**Why wrong:** Extensible = true without interface provides no value and can cause issues.

### ❌ WRONG: Non-Extensible with Interface

```al
// WRONG: Has interface but not extensible
enum 50107 "Payment Method" implements IPaymentProcessor
{
    Extensible = false;  // ❌ Wrong: has interface but not extensible

    value(1; Cash) 
    { 
        Implementation = IPaymentProcessor = CashProcessor;
    }
}
```

**Why wrong:** Defeats the purpose of interface pattern - others can't add implementations.

## Blank Value Pattern

**Always include blank value when appropriate:**

```al
enum 50108 "Priority Level"
{
    Extensible = false;

    value(0; " ") { Caption = ' '; }  // ✅ Blank option for "not set"
    value(1; Low) { Caption = 'Low'; }
    value(2; Medium) { Caption = 'Medium'; }
    value(3; High) { Caption = 'High'; }
    value(4; Critical) { Caption = 'Critical'; }
}
```

**Blank value allows:**
- "Not set" state
- Optional enum fields
- Clear default value

## Converting Options to Enums

**Old option field (deprecated):**
```al
field(10; Status; Option)
{
    OptionMembers = " ",Open,"Pending Approval",Approved,Rejected;
}
```

**New enum approach:**
```al
// 1. Create enum
enum 50109 "Document Status"
{
    Extensible = false;  // No interface = not extensible

    value(0; " ") { Caption = ' '; }
    value(1; Open) { Caption = 'Open'; }
    value(2; "Pending Approval") { Caption = 'Pending Approval'; }
    value(3; Approved) { Caption = 'Approved'; }
    value(4; Rejected) { Caption = 'Rejected'; }
}

// 2. Update field
field(10; Status; Enum "Document Status")
{
    Caption = 'Status';
}

// 3. Conversion procedure for data migration
procedure ConvertOptionToEnum(OptionValue: Integer): Enum "Document Status"
begin
    case OptionValue of
        0: exit("Document Status"::" ");
        1: exit("Document Status"::Open);
        2: exit("Document Status"::"Pending Approval");
        3: exit("Document Status"::Approved);
        4: exit("Document Status"::Rejected);
    end;
end;
```

## AI Specialist Enforcement

When specialists see enum creation:

**Creating enum with interface:**
```
"I see you're creating an enum that implements an interface. According to iFacto 
standards, this enum should be:

Extensible = true;

This allows others to add values with their own interface implementations."
```

**Creating enum without interface:**
```
"This enum doesn't implement an interface. According to iFacto standards, it should be:

Extensible = false;

This clearly indicates it's a closed list of values."
```

**Enum extensibility doesn't match interface:**
```
"⚠️ Extensibility mismatch:
- Has interface: Extensible should be true
- No interface: Extensible should be false

iFacto standard: Extensible = true ONLY when enum implements an interface."
```

## Code Review Checklist

- [ ] Does enum implement an interface?
  - Yes → Extensible = true
  - No → Extensible = false
- [ ] Is blank value included when appropriate?
- [ ] Are captions clear and user-friendly?
- [ ] If interface implementation, are all implementations defined?
- [ ] Is enum name descriptive and follows naming conventions?

## Related Guidelines

- [Naming Conventions](ifacto-naming-conventions.md) for enum naming patterns
- [Single Object Per File](ifacto-single-object-per-file.md) for enum file structure
- [Meth Codeunit Pattern](ifacto-meth-codeunit-pattern.md) for interface implementation patterns

---

**This is an iFacto-specific rule.** Standard BC guidance is less prescriptive about enum extensibility. iFacto requires **explicit decision based on interface implementation** for consistency across all projects.
