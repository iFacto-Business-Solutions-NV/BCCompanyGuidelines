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

## 🚨 CRITICAL: How `ask_bc_expert` Works

**The `ask_bc_expert` tool does NOT return a ready-made answer.** It returns specialist context and guidelines for YOU to apply. Understanding this is essential.

### Tool Behavior (Interactive Mode — default)
When you call `mcp_bc-code-intel_ask_bc_expert`, it returns:
- The specialist's full persona definition and expertise description
- A directive: "You ARE [specialist]. Respond directly as this specialist…"
- Relevant knowledge and guidelines

**YOU must then use this context to produce your own expert response.** The tool output is your INSTRUCTION SET, not the final answer. Never report "the specialist returned its definition" — the definition IS the specialist's contribution for you to apply.

### Tool Behavior (Autonomous Mode — RECOMMENDED for validation)
When you call `mcp_bc-code-intel_ask_bc_expert` with `autonomous_mode: true`, it returns:
- Structured JSON with `response_type: autonomous_action_plan`
- A concrete action plan with steps
- Recommended topics to check
- Specialist metadata

**Use `autonomous_mode: true` for all validation and review tasks.** It produces structured, actionable output.

### Fallback: Direct Topic Retrieval
If `ask_bc_expert` output is difficult to apply, use these tools instead:

| Tool | Use Case |
|------|----------|
| `mcp_bc-code-intel_get_bc_topic` | Get a specific guideline by topic ID |
| `mcp_bc-code-intel_find_bc_knowledge` | Search for guidelines by keyword |

**Key topic IDs for iFacto company guidelines:**
- `ifacto-error-handling-standards` — Label variables for Error/Message/Confirm
- `ifacto-meth-codeunit-pattern` — ONE public procedure per Meth codeunit
- `ifacto-single-object-per-file` — One AL object per file
- `ifacto-naming-conventions` — English-only, PascalCase
- `ifacto-enum-patterns` — Extensible = false by default
- `ifacto-upgrade-install-sync` — Install/Upgrade codeunit sync
- `ifacto-documentation-standards` — Documentation structure

## Mandatory Consultation Order

### Priority 1: waldo — Company Standards (ALWAYS FIRST)
**MANDATORY for every consultation. No exceptions.**

Call `mcp_bc-code-intel_ask_bc_expert` with:
- `preferred_specialist`: `waldo-company`
- `autonomous_mode`: `true`
- `question`: A specific question about iFacto company guidelines for the task at hand

After receiving the response, **apply the guidelines yourself** to produce explicit ✅/❌ verdicts or implementation guidance. The tool gives you the rules — you enforce them.

**Alternative (more direct):** Call `mcp_bc-code-intel_get_bc_topic` for each relevant guideline topic ID listed above, then apply those guidelines to the code.

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

Call `mcp_bc-code-intel_ask_bc_expert` with `autonomous_mode: true` and the appropriate specialist ID.

### Priority 3: Additional Specialists (if needed)
For complex tasks, consult multiple specialists sequentially. Examples:
- Complex feature: waldo → Sam (code) → Alex (architecture) → Peter (performance)
- Code review: waldo → Roger (quality) → Quinn (testing)

## Recommended Call Patterns

### For Code Review / Validation
```
Tool: mcp_bc-code-intel_ask_bc_expert
Parameters:
  question: "Validate this code against iFacto company guidelines: [brief code summary]"
  preferred_specialist: "waldo-company"
  autonomous_mode: true
```
Then: Apply the returned action plan to produce your own ✅ PASS / ❌ FAIL report.

### For Implementation Guidance
```
Tool: mcp_bc-code-intel_ask_bc_expert
Parameters:
  question: "What iFacto guidelines apply to [object type / pattern]?"
  preferred_specialist: "waldo-company"
  autonomous_mode: true
```
Then: Use the returned guidelines as constraints for your implementation.

### For Direct Guideline Retrieval (fastest, no persona)
```
Tool: mcp_bc-code-intel_get_bc_topic
Parameters:
  topic_id: "ifacto-error-handling-standards"
```
Then: Read the topic content and apply it directly.

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

**Remember:** After receiving specialist context from the tool, YOU produce this output by applying the guidelines to the actual code. The tool provides the rules — you do the validation.

## Important
- **waldo is ALWAYS first** — company guidelines have HIGHEST priority
- **Company standards override general BC best practices** when they conflict
- **Never skip waldo** even if the task seems purely technical
- **Always use `autonomous_mode: true`** for validation and review tasks
- **The tool returns context, not answers** — you must apply the guidelines yourself
- **If tool output seems like a "definition dump"**, use `get_bc_topic` fallback for direct guideline content
