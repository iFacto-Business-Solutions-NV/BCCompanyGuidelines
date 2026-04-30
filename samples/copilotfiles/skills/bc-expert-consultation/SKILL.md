---
name: bc-expert-consultation
description: 'Consult bc-code-intel specialists following mandatory priority order: waldo (company standards) FIRST, then domain specialists. Use when: writing AL code, reviewing code, designing solutions, documenting BC projects, any task that must follow iFacto company guidelines.'
---

# bc-code-intel Expert Consultation

## When to Use
- Before writing any AL code (company standards check)
- During code review (compliance validation)
- Solution design (architectural guidance)
- Documentation (structure and conventions)
- Any BC development task requiring iFacto standards

## Mandatory Consultation Order

### Priority 1: waldo — Company Standards (ALWAYS FIRST)
**MANDATORY for every consultation. No exceptions.**

Call `mcp_bc-code-intel_ask_bc_expert` with specialist `waldo-company`:
- Ask for ALL applicable iFacto company guidelines for the task at hand
- Topics include: error handling (label variables), Meth codeunit pattern, code organization (one object per file), English-only identifiers, enum extensibility rules, naming conventions, documentation standards
- Record each guideline with ✅ (will follow) or note specific applicability

### Priority 2: Domain Specialist (task-specific)
After waldo, consult the appropriate specialist:

| Task | Specialist | ID |
|------|-----------|-----|
| Writing AL code | Sam | `sam-coder` |
| Code review | Roger | `roger-reviewer` |
| Architecture/design | Alex | `alex-architect` |
| Documentation | Taylor | `taylor-docs` |
| Testing | Quinn | `quinn-tester` |
| Performance | Peter | `peter-perf` |
| Debugging | Dean | `dean-debug` |
| Integration | Jordan | `jordan-integrator` |

Call `mcp_bc-code-intel_ask_bc_expert` with the appropriate specialist ID.

### Priority 3: Additional Specialists (if needed)
For complex tasks, consult multiple specialists sequentially. Examples:
- Complex feature: waldo → Sam (code) → Alex (architecture) → Peter (performance)
- Code review: waldo → Roger (quality) → Quinn (testing)

## Output Format
Structure findings as:
```
## 🏢 Company Standards (waldo)
- ✅ Error handling: Label variables required
- ✅ Code organization: One object per file
- ✅ Meth pattern: ONE public procedure
- [... all applicable guidelines]

## 🎯 {Specialist} Guidance
- {Domain-specific recommendations}
- {Patterns to follow}
- {Anti-patterns to avoid}
```

## Compliance Validation Mode
When used for review (not guidance), format as pass/fail:
```
## 🏢 Company Standards Validation
- ✅ PASS: Error handling uses label variables
- ❌ FAIL: Hardcoded error message on line 42
- ✅ PASS: Single object per file
- Score: X/Y PASSED
```

## Important
- **waldo is ALWAYS first** — company guidelines have HIGHEST priority
- **Company standards override general BC best practices** when they conflict
- **Never skip waldo** even if the task seems purely technical
