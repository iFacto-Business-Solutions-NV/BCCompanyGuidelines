---
methodology_id: "company-code-review"
name: "iFacto Company Code Review Process"
type: "process"
domains: ["quality", "standards", "team-collaboration"]
phases: ["preparation", "review", "feedback", "approval"]
applicable_to: ["all-bc-projects"]
---

# iFacto Company Code Review Process

## What Makes This Company-Specific

While standard BC development includes code reviews, **iFacto enforces** specific company guideline validation as a **mandatory part** of every code review. This methodology integrates all 7 iFacto company standards into the review process through systematic validation using bc-code-intel MCP specialists.

## Overview

Our company code review process ensures all BC development meets quality standards and follows **iFacto company conventions** before merging into main branches. 

**Key Principle:** Reviews are coordinated with bc-code-intel specialists who have access to all company guidelines and BC best practices - reviewers delegate validation to specialists rather than enforcing rules directly.

## iFacto Company Guidelines Validation (MANDATORY)

Every code review MUST validate against ALL applicable iFacto company standards:

### 1. ✅ Error Handling Standards (CRITICAL - MANDATORY)
**Reference:** [ifacto-error-handling-standards.md](../topics/ifacto-error-handling-standards.md)

- [ ] ALL Error() calls use label variables (no hardcoded strings)
- [ ] ALL Message() calls use label variables
- [ ] ALL Confirm() calls use label variables
- [ ] Label variables follow naming pattern: `<Context>Err`, `<Context>Qst`, `<Context>Msg`
- [ ] Comment attributes included for placeholders

### 2. ✅ Meth Codeunit Pattern (MANDATORY for business logic)
**Reference:** [ifacto-meth-codeunit-pattern.md](../topics/ifacto-meth-codeunit-pattern.md)

- [ ] Business logic separated into Meth codeunits (if applicable)
- [ ] Exactly ONE public procedure per Meth codeunit
- [ ] All other procedures are local
- [ ] Meth codeunits called ONLY from tables (not pages/reports)
- [ ] Integration events included (OnBefore/OnAfter)

### 3. ✅ Single Object Per File (MANDATORY)
**Reference:** [ifacto-single-object-per-file.md](../topics/ifacto-single-object-per-file.md)

- [ ] Each .al file contains exactly ONE object/extension
- [ ] File naming follows pattern: `<ObjectType> <ID> <Name><Suffix>.al`
- [ ] No multiple objects bundled in single file

### 4. ✅ Naming Conventions (MANDATORY)
**Reference:** [ifacto-naming-conventions.md](../topics/ifacto-naming-conventions.md)

- [ ] PascalCase used for all identifiers
- [ ] English-only identifiers (no Dutch or other languages)
- [ ] English-only Caption properties (translations via .xlf only)
- [ ] Temporary records prefixed with "Temp"
- [ ] Variable declaration order followed: Record → Report → Codeunit → others
- [ ] Procedures use descriptive verb-noun patterns

### 5. ✅ Enum Patterns (MANDATORY for enum creation)
**Reference:** [ifacto-enum-patterns.md](../topics/ifacto-enum-patterns.md)

- [ ] Extensible = true ONLY when enum implements interface
- [ ] Extensible = false for enums without interfaces
- [ ] Blank value included when appropriate
- [ ] Extensibility decision documented

### 6. ✅ Upgrade and Install Codeunit Synchronization (CRITICAL)
**Reference:** [ifacto-upgrade-install-sync.md](../topics/ifacto-upgrade-install-sync.md)

- [ ] If upgrade codeunit exists, install codeunit also exists
- [ ] Upgrade logic exposed as PUBLIC procedures
- [ ] Install codeunit calls upgrade codeunit procedures
- [ ] Fresh installation and upgrade paths produce consistent data states

### 7. ✅ Documentation Standards (MANDATORY)
**Reference:** [ifacto-documentation-standards.md](../topics/ifacto-documentation-standards.md)

- [ ] Docs/ folder structure exists (if applicable)
- [ ] README.md provides navigation hub
- [ ] CHANGELOG.md uses weekly grouping (YYYY.WW)
- [ ] JIRA keys are clickable links
- [ ] Cross-references use markdown links
- [ ] Test documentation describes manual procedures (not code)

## General Code Quality (BC Best Practices)

Beyond company guidelines, validate standard BC practices:

### ✅ Code Compilation
- [ ] No compilation errors or warnings
- [ ] NoImplicitWith compliance (per app.json features)

### ✅ Object IDs
- [ ] Object IDs within project's assigned range (check app.json)
- [ ] No duplicate object IDs

### ✅ Testing
- [ ] Unit tests included for business logic (if applicable)
- [ ] Manual testing performed
- [ ] Test scenarios documented

### ✅ Performance
- [ ] No unnecessary database calls in loops
- [ ] Appropriate use of temporary tables
- [ ] SetLoadFields used where applicable
- [ ] Filtering applied before complex operations

## Review Process Workflow

### Phase 1: Pre-Review Validation (Developer - RECOMMENDED)

**Before creating PR, validate using bc-code-intel specialists:**

#### Step 1: Company Standards Validation

```
Call: mcp_bc-code-intel_ask_bc_expert

Question: "Using iFacto company standards, perform COMPREHENSIVE validation 
of this code against ALL applicable company guidelines:

Files: [list files]
Code: [provide code]

For EACH applicable guideline, provide explicit ✅ PASS or ❌ FAIL with 
specific line references and corrections needed."

Preferred specialist: "waldo-company"
```

**waldo will validate ALL 7 company guidelines** with explicit results.

#### Step 2: Fix Violations

Address all company guideline violations before creating PR.

#### Step 3: Create Pull Request

1. Ensure all company guideline validations pass
2. Create PR with descriptive title and description
3. Link related work items (JIRA issues)
4. Request review from team member

---

### Phase 2: Code Review (Reviewer)

#### Step 1: Read the Code

Use `read_file` to examine all files in the PR scope.

#### Step 2: Consult Company Standards Specialist

**CRITICAL: Always start with iFacto company layer validation**

```
Call: mcp_bc-code-intel_ask_bc_expert

Question: "Using iFacto company standards, review this code:

Files: [list all files in PR]
Code: [provide complete code]

Validate against ALL applicable iFacto company guidelines. 
For EACH guideline, provide explicit ✅ PASS or ❌ FAIL with 
line references and specific corrections."

Preferred specialist: "waldo-company"
```

**waldo's response will include:**
- ✅/❌ for each applicable company guideline
- Specific line numbers for violations
- Exact corrections needed
- MANDATORY vs RECOMMENDED issues

#### Step 3: Consult General BC Quality Specialist

```
Call: mcp_bc-code-intel_ask_bc_expert

Question: "Review this BC AL code for general quality, maintainability, 
and BC best practices:

Code: [provide complete code]

Check: performance, security, patterns, readability, maintainability."

Preferred specialist: "roger-reviewer"
```

**Roger's response will cover:**
- Code quality issues
- Performance concerns
- Security considerations
- Maintainability improvements

#### Step 4: Consolidate Findings

Provide structured feedback:

```markdown
# 🔍 Code Review - [PR Title]

## 🏢 iFacto Company Standards (via waldo)

[Paste waldo's validation results with ✅/❌ for each guideline]

## 🎯 BC Best Practices (via Roger)

[Paste Roger's quality review findings]

## 🚨 Must Fix (MANDATORY)
- [Company guideline violations from waldo]

## ⚠️ Should Fix (High Priority)
- [Important improvements from specialists]

## 💡 Consider (Optional Enhancements)
- [Nice-to-have suggestions]

## ✅ Approval Status
- [ ] Company guidelines: [PASS/FAIL]
- [ ] BC best practices: [PASS/FAIL with minor issues]
- [ ] Testing: [PASS/FAIL]

**Decision**: [Approve / Request Changes]
```

#### Step 5: Post Review Comments

- Reference specific line numbers
- Link to relevant guideline topic files
- Mark MANDATORY issues clearly
- Provide exact corrections for violations

---

### Phase 3: Address Feedback (Developer)

1. Fix all MANDATORY company guideline violations first
2. Address high-priority BC best practice issues
3. Consider optional enhancements
4. Re-validate with waldo if company guideline changes made
5. Update PR and request re-review
6. Mark conversations as resolved

---

### Phase 4: Approval (Team)

**Approval Criteria:**
1. ✅ ALL company guidelines pass (MANDATORY)
2. ✅ No critical BC best practice violations
3. ✅ All reviewer conversations resolved
4. ✅ CI/CD pipeline passes
5. ✅ Minimum one approval from team member

**Merge when all criteria met.**

## Review Standards & Priorities

### Priority 1: iFacto Company Guidelines (MANDATORY)

**Zero tolerance for violations** - These MUST be fixed before merge:

- ❌ Hardcoded error/message/confirmation text (must use label variables)
- ❌ Multiple public procedures in Meth codeunits (must be exactly ONE)
- ❌ Multiple objects in single .al file (must be one per file)
- ❌ Non-English identifiers or captions (must be English-only)
- ❌ Incorrect enum extensibility (must match interface implementation)
- ❌ Missing install codeunit when upgrade codeunit exists
- ❌ Missing or incomplete required documentation

**All 7 company guidelines validated via waldo-company specialist.**

### Priority 2: BC Best Practices (Standard Development)

**Should be addressed** - Can be fixed in follow-up if minor:

- ⚠️ Code readability and maintainability issues
- ⚠️ Performance concerns (unnecessary loops, missing SetLoadFields)
- ⚠️ Security issues (hardcoded credentials, data exposure)
- ⚠️ Missing error handling or edge cases

**General BC quality validated via roger-reviewer specialist.**

### Priority 3: Enhancements (Optional)

**Nice to have** - Can be deferred:

- 💡 Code simplification opportunities
- 💡 Additional comments for clarity
- 💡 Refactoring suggestions
- 💡 Additional test coverage

## Communication Guidelines

### When Providing Feedback

**Always reference specialists and guidelines:**
- "❌ Company guideline violation (via waldo): [specific issue with link to topic]"
- "⚠️ BC best practice concern (v (How to Use)

**Company Standards (Consult First):**
- **waldo-company** - Validates ALL 7 iFacto company guidelines
  - Use for: Pre-review validation, company standards compliance checks
  - Returns: Explicit ✅/❌ for each guideline with line references

**BC Best Practices:**
- **roger-reviewer** - General BC code quality and standards
  - Use for: Quality review, maintainability, BC patterns
  - Returns: Code quality assessment with improvement suggestions

- **sam-coder** - AL coding patterns and implementation
  - Use for: Technical validation, implementation patterns
  - Returns: Technical feedback on AL code quality

**Architecture & Design:**
- **alex-architect** - Architecture and design patterns
  - Use for: Complex design decisions, architectural guidance

### Additional Resources

- **Object ID Management**: Check project's app.json for assigned range
- **BC Documentation**: Microsoft Learn for standard BC patterns
- **Code Examples**: Reference existing codebase for established patterns

## Escalation & Dispute Resolution

### For Company Guideline Violations

**Company guidelines are MANDATORY and non-negotiable.**

If developer questions a company guideline violation:

1. **Consult the specific guideline topic file** - Detailed explanation with examples
2. **Ask waldo-company for clarification** - Expert interpretation via MCP
3. **Team lead escalation** - ONLY for guideline interpretation questions (rare)
4. **Document exceptions** - Any approved exceptions must be documented (extremely rare)

**Note:** Company guidelines are not suggestions - they are mandatory requirements.

### For BC Best Practice Disputes

If disagreements arise on non-mandatory best practice items:

1. **Discuss with reviewer first** - Often resolves through conversation
2. **Consult specialist again** - Get additional context from Roger/Sam via MCP
3. **Team lead decision** - If still unresolved
4. **Document decision** - Record for future reference

## Example Review Workflow

### Developer Pre-Review Example

```
Developer: "Using iFacto company standards, perform COMPREHENSIVE validation 
against ALL company guidelines for:

Files: Codeunit 50100 Customer Validation.al
[Provides complete code]

Check every applicable guideline with explicit ✅/❌ results."

waldo-company specialist via MCP:
"Performing comprehensive company guidelines validation...

✅ File Organization - Single Object Per File: PASS
✅ File Naming Convention: PASS (matches pattern)
❌ Error Handling - Label Variables: FAIL
   Line 42: Error('Customer not found') - hardcoded string
   CORRECTION: Use label variable with Err suffix
❌ Naming - English-only identifiers: FAIL  
   Line 15: Variable 'klantNaam' - Dutch identifier
   CORRECTION: Rename to 'customerName'
✅ Meth Pattern: PASS (one public procedure)
✅ Naming - PascalCase: PASS

Company Guidelines: 3/5 PASSED - 2 MANDATORY VIOLATIONS"

Developer: [Fixes violations, re-validates, then creates PR]
```

### Reviewer Code Review Example

```markdown
# 🔍 Code Review - Customer Validation Enhancement

## 🏢 iFacto Company Standards (waldo validation)

✅ Error Handling: PASS - All Error() calls use label variables
✅ Meth Pattern: PASS - Exactly one public procedure
✅ Naming: PASS - English-only PascalCase identifiers  
✅ File Organization: PASS - One object per file
✅ All company guidelines: PASSED

## 🎯 BC Best Practices (Roger review)

✅ Code quality: Good structure and readability
⚠️ Performance: Consider using SetLoadFields on line 28
✅ Error handling: Appropriate validation
💡 Enhancement: Could add OnBefore event for extensibility

## 🚨 Must Fix
None - all company guidelines pass

## ⚠️ Should Fix  
- Line 28: Add SetLoadFields before Get() for performance

## 💡 Consider
- Add OnBeforeValidate event for extensibility

## ✅ Approval Status
- [x] Company guidelines: PASS
- [x] BC best practices: PASS (minor performance suggestion)
- [x] Testing: Manual testing completed

**Decision: ✅ APPROVED** (address performance suggestion in follow-up)
```

---

**This methodology ensures systematic validation of both iFacto company standards and BC best practices through coordination with bc-code-intel specialists. Company guidelines validation is MANDATORY - no exceptions.*
If disagreements arise on non-mandatory items:
1. Discuss with reviewer first
2. Bring to team lead if unresolved
3. Document decision for future reference

## Using bc-code-intel MCP for Reviews

**Leverage AI specialists during code review:**

1. **Pre-Review (Developer)**: Ask waldo-company to validate your code against ALL company guidelines before submitting PR
2. **During Review (Reviewer)**: Use waldo-company for systematic guideline validation
3. **Technical Questions**: Consult roger-reviewer or sam-coder for BC best practices
4. **Architecture Decisions**: Involve alex-architect for design questions

**Example review workflow:**
```
1. Developer: "waldo, validate this codeunit against all company guidelines"
2. waldo provides comprehensive ✅/❌ validation results
3. Developer fixes all violations before creating PR
4. Reviewer verifies fixes and validates general code quality
5. PR approved when both company guidelines and best practices pass
```

---

**This process ensures code quality while fostering team collaboration and continuous improvement. Company guidelines validation is MANDATORY for all code reviews - no exceptions.*
- **Company Naming Standards**: See `ifacto-naming-conventions.md`
- **Object ID Tracking**: Internal wiki or shared document
- **Code Examples**: Reference existing codebase for patterns
- **BC Best Practices**: Microsoft Learn documentation

## Escalation

If disagreements arise:
1. Discuss with reviewer first
2. Bring to team lead if unresolved
3. Document decision for future reference

---

*This process ensures code quality while fostering team collaboration and continuous improvement.*
