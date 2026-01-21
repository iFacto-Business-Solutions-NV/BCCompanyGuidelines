# BCCompanyGuidelines - Complete Workspace Alignment & Validation Prompt

**Purpose:** Systematically review, validate, and update ALL files in the BCCompanyGuidelines workspace to ensure compliance with bc-code-intel MCP server standards and iFacto company layer requirements.

## 🎯 Your Mission

You are conducting a comprehensive audit and alignment of the iFacto company knowledge layer for the bc-code-intel MCP server. This workspace extends the [bc-code-intel MCP server](https://github.com/JeremyVyska/bc-code-intelligence-mcp) with company-specific Business Central development guidelines.

**CRITICAL CONTEXT:**
- This is a **meta-repository** - it creates company-specific BC development guidelines, NOT BC code itself
- It provides the **company layer** for bc-code-intel (base → company → project)
- Focus on **delta guidelines** - only iFacto-specific rules that differ from standard BC practices
- All content must be **MCP-compatible** for VS Code integration

## 📋 Step-by-Step Execution Plan

### Phase 0: Read bc-code-intel SOURCE CODE

**🚨 CRITICAL: Read the ACTUAL SOURCE CODE to understand what bc-code-intel requires!**

**DO NOT INVENT REQUIREMENTS. DO NOT ASSUME. READ THE CODE.**

Use the `github_repo` tool to read the ACTUAL implementation in bc-code-intel:

1. **Read the Topic Loading Source Code**
   ```
   Query: "loadAtomicTopics loadTopics function implementation directory scanning YAML parsing"
   Repo: "JeremyVyska/bc-code-intelligence-mcp"
   ```
   
   Find in the SOURCE CODE:
   - What directories does it scan? (domains/, topics/, both?)
   - What file extensions does it look for?
   - How does it parse YAML frontmatter?
   - What happens if YAML is missing?
   - What fields does it actually READ and USE?

2. **Read the YAML Schema Definition (Zod)**
   ```
   Query: "AtomicTopicFrontmatterSchema z.object definition Zod schema"
   Repo: "JeremyVyska/bc-code-intelligence-mcp"
   ```
   
   Find the EXACT schema:
   - Which fields are `.required()` vs `.optional()`?
   - What are the actual type definitions?
   - Any `.refine()` or custom validation?
   - Default values?

3. **Read the Validation Logic**
   ```
   Query: "validateTopic function topic validation frontmatter checking"
   Repo: "JeremyVyska/bc-code-intelligence-mcp"
   ```
   
   Understand:
   - What validation is performed AFTER schema parsing?
   - Which fields cause topics to be rejected?
   - Error messages for missing fields?

4. **Read Specialist and Methodology Loading Code**
   ```
   Query: "loadSpecialists loadMethodologies specialist_id methodology_id parsing"
   Repo: "JeremyVyska/bc-code-intelligence-mcp"
   ```
   
   Find:
   - Required fields for specialists
   - Required fields for methodologies
   - Directory structure expectations
   - File naming conventions

5. **Document ONLY What You Found in Source Code**
   
   List ONLY requirements found in actual code:
   - Required fields (based on code, not assumptions)
   - Required directories (based on code scanning logic)
   - Optional fields (marked `.optional()` in schema)
   - iFacto additions (fields not in bc-code-intel schema)

**IF bc-code-intel SOURCE CODE doesn't require it, WE DON'T NEED IT.**
**IF you can't find it in the SOURCE CODE, DON'T ASSUME IT EXISTS.**

### Phase 1: Discovery & Assessment

1. **Read All Current Files**
   - Read all topic files in `bc-company-guidelines/topics/` or `bc-company-guidelines/domains/`
   - Read specialist files in `bc-company-guidelines/specialists/`
   - Read methodology files in `bc-company-guidelines/methodologies/`
   - Read documentation files (README.md, DELTA-SUMMARY.md, etc.)

2. **Assess Current State Against bc-code-intel Requirements**
   - Compare current YAML frontmatter with bc-code-intel schema (from Phase 0)
   - Identify fields that are iFacto additions vs bc-code-intel standard
   - Check directory structure matches bc-code-intel expectations
   - Verify all markdown files have valid YAML frontmatter blocks
   - List topics with missing or incomplete frontmatter

### Phase 2: YAML Frontmatter Validation & Correction

**🚨 CRITICAL: Use ONLY what Phase 0 found in bc-code-intel SOURCE CODE!**

**DO NOT add fields that bc-code-intel doesn't read. DO NOT invent requirements.**

For each topic file in `bc-company-guidelines/topics/` (or `bc-company-guidelines/domains/`):

1. **Verify YAML Frontmatter Has Required Fields**
   
   Based on Phase 0 SOURCE CODE analysis, add ONLY the fields that bc-code-intel:
   - Marks as `.required()` in the Zod schema, OR
   - Checks for in validation logic (like `validateTopic()`)
   
   **If Phase 0 showed the schema has NO required fields, then frontmatter is optional.**

2. **Add Optional Fields ONLY If They Help Discovery**
   
   Based on Phase 0 SOURCE CODE (Zod schema with `.optional()`):
   - ONLY add fields that exist in bc-code-intel's schema
   - ONLY add fields that bc-code-intel actually reads/uses
   - DO NOT add custom fields unless they're iFacto-specific additions
   
   **Example fields to CHECK in Phase 0 source code:**
   - Does bc-code-intel read `title`? Add it.
   - Does bc-code-intel read `domain`? Add it.
   - Does bc-code-intel read `tags`? Add it.
   - Does bc-code-intel read `bc_versions`? Add it if found.
   - Does bc-code-intel read `difficulty`? Add it if found.
   - etc.

3. **Fix Missing or Invalid Frontmatter**
   - Add ONLY fields found in bc-code-intel source code
   - Ensure proper YAML syntax
   - Use field names EXACTLY as defined in bc-code-intel schema

### Phase 3: Content Structure Validation

For each topic file, ensure it follows this structure:

```markdown
---
title: "Topic Title"
domain: "business-central"
tags: ["tag1", "tag2"]
bc_versions: "14+"
difficulty: "intermediate"
---
# iFacto <Topic Name> Standards

**Company Standard**

## What Makes This Company-Specific

[Clear explanation of how this differs from standard BC practices]

## The Rule

[Unambiguous statement of the iFacto rule]

## Examples

### ✅ CORRECT - Following iFacto Standard
```al
// Code example showing the RIGHT way
```

### ❌ WRONG - Violates iFacto Standard
```al
// Code example showing what NOT to do
```

## Why This Matters (iFacto Perspective)

[Business/technical rationale specific to iFacto]

## Enforcement

[How AI specialists should enforce this rule]

## Related Guidelines

[Links to related topic files]
```

**Validation Checklist for Each Topic:**
- [ ] Has proper markdown header structure
- [ ] Includes "What Makes This Company-Specific" section
- [ ] Contains clear, unambiguous rule statement
- [ ] Shows both ✅ CORRECT and ❌ WRONG code examples
- [ ] Code examples use `al` language in code fences
- [ ] Explains the "why" from iFacto perspective
- [ ] Includes enforcement guidance for AI specialists
- [ ] Cross-references related guidelines

### Phase 4: Delta-Focused Content Review

**CRITICAL PRINCIPLE: Only include iFacto-specific guidelines!**

For each topic, verify:

1. **Is This Truly Company-Specific?**
   - ✅ INCLUDE: "ALL error messages MUST use label variables" (stricter than standard)
   - ✅ INCLUDE: "Meth codeunit pattern with ONE public procedure" (iFacto pattern)
   - ✅ INCLUDE: "English-only identifiers" (company standard)
   - ❌ EXCLUDE: "Use meaningful variable names" (generic advice)
   - ❌ EXCLUDE: "Follow AL naming conventions" (standard BC)

2. **Does the "What Makes This Company-Specific" Section Clearly Explain the Delta?**
   - If not, enhance it to clearly distinguish from base BC practices

3. **Are Code Examples Realistic and iFacto-Specific?**
   - Not generic "foo/bar" examples
   - Show actual iFacto patterns and anti-patterns

### Phase 5: Specialist Files Validation

Review files in `bc-company-guidelines/specialists/`:

1. **Verify Specialist Profiles Follow bc-code-intel Patterns**
   - Check against bc-code-intel specialist structure
   - Ensure personality, expertise, and approach are well-defined
   - Validate that specialists complement base bc-code-intel specialists

2. **Key Specialists to Validate**
   - **waldo-company.md** - iFacto company standards enforcer
   - **isabelle-distri.md** - Distribution/installation specialist
   - Others as present

3. **Ensure No Duplication with Base Specialists**
   - These should enhance or specialize, not duplicate base bc-code-intel specialists

### Phase 6: Methodology Files Validation

Review files in `bc-company-guidelines/methodologies/`:

1. **company-code-review.md** - iFacto code review process
   - Ensure it references company topics appropriately
   - Verify it doesn't duplicate base BC review practices
   - Check that it integrates with company specialists

### Phase 7: Cross-Reference Validation

1. **Check All Internal Links**
   - Links between topic files
   - Links from specialists to topics
   - Links from methodologies to topics and specialists

2. **Fix Broken or Missing Links**
   - Update paths if files were renamed
   - Add missing cross-references where appropriate

3. **Ensure Consistent Link Format**
   - Use relative paths within bc-company-guidelines
   - Markdown link format: `[Display Text](path/to/file.md)`

### Phase 8: Documentation Files Update

1. **bc-company-guidelines/README.md**
   - Update with current list of topics (brief summary with links)
   - Ensure it explains the delta-focused approach
   - Do NOT list detailed guidelines (that's in topics)

2. **docs/DELTA-SUMMARY.md** (if exists)
   - Update with current deltas
   - Keep it concise and high-level

3. **docs/MAINTENANCE.md** (if exists)
   - Ensure procedures are current
   - Reflect any structural changes

4. **Root README.md**
   - Ensure it links properly to bc-company-guidelines
   - Keep it high-level and introductory

### Phase 9: Compliance Verification

**Final Checklist:**

#### YAML Frontmatter Compliance
- [ ] ALL topic files have YAML frontmatter at the very top
- [ ] Each topic has at minimum `title` OR `domain` field
- [ ] YAML frontmatter is valid syntax
- [ ] Tags are arrays of strings (if present)
- [ ] All fields follow bc-code-intel's optional schema

#### Directory Structure Compliance
- [ ] Topics are in `topics/` or `domains/` directory
- [ ] All topic files are markdown (.md)
- [ ] Specialists in `specialists/` directory (if present)
- [ ] Methodologies in `methodologies/` directory (if present)

#### Content Compliance
- [ ] All topics follow standard structure
- [ ] All topics have ✅ CORRECT and ❌ WRONG examples
- [ ] All topics explain "what makes this company-specific"
- [ ] All topics include enforcement guidance
- [ ] Code examples use AL language properly
- [ ] Topics are delta-focused (not generic BC advice)

#### Structure Compliance
- [ ] All files in correct directories
- [ ] No duplicate content across files
- [ ] Specialists don't reference topics directly (they use MCP)
- [ ] Documentation doesn't list specific guidelines (references MCP)
- [ ] Cross-references are working

## 🔄 Execution Instructions

1. **Work Systematically Through Each Phase**
   - **START WITH PHASE 0** - Always verify bc-code-intel requirements first
   - Complete each phase before moving to the next
   - Report findings after each phase
   - **STOP and report to user** if Phase 0 findings contradict this prompt
   - Ask for confirmation before making widespread changes

2. **Use Efficient Tool Calls**
   - Parallelize file reads when possible
   - Use multi_replace_string_in_file for multiple edits
   - Batch similar operations

3. **Provide Progress Updates**
   - After each phase, summarize what was found and what will be fixed
   - Highlight critical issues (missing YAML frontmatter, etc.)
   - Show metrics (X of Y files validated, N issues found)

4. **Create Summary Report**
   - At the end, provide a comprehensive summary:
     - Phase 0 findings: What bc-code-intel actually requires
     - Discrepancies found between prompt assumptions and bc-code-intel reality
     - Total topics validated
     - YAML frontmatter issues fixed
     - Directory structure validated
     - Content improvements applied
     - Any remaining items for manual review

5. **Version Control Readiness**
   - Prepare a summary of all changes for commit message
   - Group changes logically
   - Highlight breaking changes if any

## ⚠️ Critical Rules - NEVER VIOLATE

1. **ALWAYS start with Phase 0** - Verify bc-code-intel requirements before making assumptions
2. **NEVER remove YAML frontmatter** - Only add or fix it
3. **NEVER add standard BC best practices** - Only iFacto-specific deltas
4. **NEVER create AL code files** - This is a meta-repository for guidelines
5. **NEVER list specific guidelines in template/documentation files** - They should reference MCP
6. **READ THE SOURCE CODE FIRST** - Phase 0 means reading bc-code-intel's actual implementation, not guessing
2. **IF bc-code-intel doesn't require it, WE DON'T NEED IT** - Don't invent requirements like "knowledge-config.json"
3. **ONLY add fields that exist in bc-code-intel's schema** - Check the Zod definition in source code
4. **NEVER remove YAML frontmatter** - Only add or fix it based on source code requirements
5. **NEVER add standard BC best practices** - Only iFacto-specific deltas
6. **NEVER create AL code files** - This is a meta-repository for guidelines
7. **NEVER list specific guidelines in template/documentation files** - They should reference MCP
8. **NEVER create shortcuts that bypass detailed topics** - Topics are the single source of truth
9. **NEVER auto-commit to git** - Let the user review and commit
10 ✅ 100% of topic files have valid YAML frontmatter
- ✅ All topics in proper directory structure (topics/ or domains/)
- ✅ All topics follow the standard structure
- ✅ All topics are clearly delta-focused (iFacto-specific)
- ✅ All code examples show ✅ CORRECT and ❌ WRONG patterns
- ✅ All cross-references are working
- ✅ bc-code-intel MCP server can successfully index all topics
- ✅ No orphaned files or broken references
- ✅ Documentation is accurate and current

## 📚 Reference Materials

- **bc-code-intel MCP server:** https://github.com/JeremyVyska/bc-code-intelligence-mcp
- **Company Layer Setup Guide:** https://github.com/JeremyVyska/bc-code-intelligence-mcp/blob/main/examples/company-layer-setup.md
- **Copilot Instructions:** `.github/copilot-instructions.md` (study this THOROUGHLY before starting)

---

**BEGIN EXECUTION**

Start with **Phase 0: Analyze bc-code-intel Documentation**. Use `github_repo` tool to verify actual requirements before proceeding to Phase 1.
