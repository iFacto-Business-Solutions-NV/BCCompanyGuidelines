# GitHub Copilot Instructions for BCCompanyGuidelines Repository

## 🎯 Repository Purpose

**THIS IS A META-REPOSITORY** - It creates company-specific BC development guidelines for bc-code-intel MCP server, not BC code itself.

### What This Repository Does

This repository maintains **iFacto's company-layer knowledge** for the bc-code-intel MCP server. It contains:

- **Delta guidelines** - Only rules that differ from or extend base BC best practices
- **Company-specific patterns** - iFacto architectural patterns (like Meth codeunits)
- **Mandatory standards** - Company-wide enforcements stricter than standard BC
- **MCP-compatible format** - Structured for bc-code-intel consumption in VS Code

### What This Repository Does NOT Do

- ❌ Does NOT contain actual Business Central AL code
- ❌ Does NOT contain project-specific guidelines (those go in individual BC projects)
- ❌ Does NOT duplicate standard BC best practices (bc-code-intel base layer has those)
- ❌ Does NOT use GitHub Copilot workspace instructions (it's for bc-code-intel MCP)

## � Understanding bc-code-intel Foundation

**CRITICAL: Before creating or modifying guidelines, agents, or specialists in this repository, you MUST understand how bc-code-intel works.**

### bc-code-intel MCP Server

This repository extends the [bc-code-intel MCP server](https://github.com/JeremyVyska/bc-code-intelligence-mcp), which provides:
- **Base BC knowledge layer** - Standard Business Central best practices and patterns
- **Specialist AI personas** - Expert BC developers with distinct expertise areas
- **Topic-based guidance** - Searchable, categorized BC development knowledge
- **MCP protocol integration** - Seamless VS Code integration for AI-assisted development

### Why This Matters

**Before adding any content to this repository:**
1. **Study the bc-code-intel architecture** - Understand how topics, specialists, and workflows function
2. **Review existing base knowledge** - Ensure you're not duplicating what bc-code-intel already provides
3. **Follow bc-code-intel patterns** - Match the structure and format expectations
4. **Understand the knowledge layers** - Know where company guidelines fit in the overall system

### Key bc-code-intel Concepts to Understand

- **Knowledge Layers**: Base → Company → Project (this repo is the Company layer)
- **Topic Discovery**: How YAML frontmatter enables MCP indexing and search
- **Specialist Architecture**: How AI personas are structured and invoked
- **MCP Protocol**: How VS Code communicates with the bc-code-intel server

**📚 Required Reading:** Visit [bc-code-intel repository](https://github.com/JeremyVyska/bc-code-intelligence-mcp) and review its documentation, examples, and implementation patterns before making changes here.

## �📋 Critical Context for AI Assistants

### Project Type: bc-code-intel MCP Company Knowledge Layer

When working on this repository, you are:
- ✅ Creating/maintaining guidelines that bc-code-intel will load and apply
- ✅ Writing documentation for developers who will use these guidelines
- ✅ Ensuring MCP server compatibility (proper JSON metadata, markdown format)
- ✅ Focusing on **iFacto-specific deviations** from standard BC practices

### Key Architecture

```
BCCompanyGuidelines Repository (YOU ARE HERE)
    ↓ produces
Company Layer Knowledge for bc-code-intel
    ↓ used by
bc-code-intel MCP Server (running in VS Code)
    ↓ provides guidance to
BC Developers building actual BC applications
```

## 🏗️ Repository Structure & Rules

### Core Files & Directories

```
BCCompanyGuidelines/
├── .vscode/
│   └── settings.json              # bc-code-intel self-reference config
├── company-bc-guidelines/         # THE MAIN CONTENT
│   ├── knowledge-config.json      # MCP metadata (CRITICAL)
│   ├── README.md                  # Delta summary
│   └── topics/                    # Individual guideline files
│       ├── error-handling.md
│       ├── code-organization.md
│       ├── meth-codeunit-pattern.md
│       ├── naming-conventions.md
│       └── enum-patterns.md
├── docs/                          # Supporting documentation
│   ├── DELTA-SUMMARY.md           # Quick reference
│   ├── MAINTENANCE.md             # Update procedures
│   └── MCP-SETUP.md               # Setup guide for developers
├── README.md                      # Repository introduction
└── TESTING-GUIDE.md               # How to test company layer
```

### When Creating/Editing Guideline Topics

**File Location:** `company-bc-guidelines/topics/<topic-name>.md`

**🚨 CRITICAL: YAML Frontmatter is MANDATORY**

**EVERY topic file MUST start with YAML frontmatter.** This is how bc-code-intel MCP server indexes and discovers topics.

**Required YAML Frontmatter Structure:**
```yaml
---
title: "Topic Title - Short Description"
domain: "business-central"
priority: critical|high|medium|low
tags: ["tag1", "tag2", "tag3"]
category: "code-quality|architecture|extensibility|project-structure"
---
```

**Example:**
```yaml
---
title: "Error Handling - Label Variables"
domain: "business-central"
priority: high
tags: ["error-handling", "label-variables", "company-standard"]
category: "code-quality"
---
```

**YAML Frontmatter Field Descriptions:**

| Field | Required | Values | Purpose |
|-------|----------|--------|---------|
| `title` | ✅ YES | "Topic - Description" | Display title for MCP server |
| `domain` | ✅ YES | "business-central" | Always use "business-central" |
| `priority` | ✅ YES | critical, high, medium, low | Importance level for MCP ranking |
| `tags` | ✅ YES | Array of strings | Searchable keywords for topic discovery |
| `category` | ✅ YES | See categories below | Topic classification |

**Valid Categories:**
- `code-quality` - Code standards, naming, formatting
- `architecture` - Architectural patterns (Meth codeunits, etc.)
- `extensibility` - Extension patterns, enum rules
- `project-structure` - File organization, folder structure

**⚠️ WITHOUT YAML FRONTMATTER, BC-CODE-INTEL CANNOT INDEX THE TOPIC!**

**Required Markdown Structure (after YAML frontmatter):**
```markdown
---
title: "Topic Title"
domain: "business-central"
priority: high
tags: ["tag1", "tag2"]
category: "code-quality"
---
# iFacto <Topic Name> Standards

**Company Standard - <Priority Level>**

## What Makes This Company-Specific

[Explain how this differs from standard BC practices]

## The Rule

[Clear, unambiguous statement of the iFacto rule]

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

**Critical Requirements:**
1. **ALWAYS include YAML frontmatter at the very top** - MCP server requirement
2. **Always show code examples** - BC developers need to see the pattern
3. **Use ✅ CORRECT and ❌ WRONG** - Make it visually obvious
4. **Explain the "why"** - Help developers understand, not just follow blindly
5. **Mark iFacto-specific** - Clearly distinguish from standard BC practices
6. **Include enforcement notes** - Help bc-code-intel apply the rule correctly

### When Updating `knowledge-config.json`

**File:** `company-bc-guidelines/knowledge-config.json`

**This is CRITICAL for bc-code-intel** - it's how the MCP server discovers and indexes topics.

**Required structure:**
```json
{
  "knowledgeBase": {
    "name": "iFacto Company Guidelines",
    "version": "<semver>",
    "description": "Company-specific BC development guidelines",
    "layer": "company",
    "priority": 50
  },
  "topics": [
    {
      "id": "unique-topic-id",
      "title": "Display Title",
      "file": "topics/filename.md",
      "category": "code-quality|architecture|extensibility|project-structure",
      "keywords": ["word1", "word2", "word3"],
      "priority": "critical|high|medium|low"
    }
  ],
  "metadata": {
    "sourceRepository": "BCCompanyGuidelines",
    "extractionDate": "YYYY-MM-DD",
    "maintainer": "iFacto Development Team",
    "updateFrequency": "quarterly"
  }
}
```

**When adding a new topic:**
1. ✅ Create the topic markdown file first
2. ✅ Add entry to `knowledge-config.json` topics array
3. ✅ Choose keywords that developers would naturally use in questions
4. ✅ Set appropriate priority based on importance
5. ✅ Update version in knowledgeBase (use semantic versioning)

**Keywords are CRITICAL** - bc-code-intel uses them to find relevant topics:
- Use terms developers will actually ask about
- Include synonyms (e.g., "error", "exception", "message")
- Think about natural language questions

## 🎨 Content Creation Guidelines

### Delta-Focused Approach (CRITICAL PRINCIPLE)

**ONLY include guidelines that are iFacto-specific.** Do NOT duplicate standard BC best practices.

**Examples:**

✅ **INCLUDE (iFacto-specific):**
- "ALL error messages MUST use label variables" (stricter than standard BC)
- "Exactly ONE object per .al file" (stricter enforcement)
- "Meth codeunit pattern with ONE public procedure" (iFacto architectural pattern)
- "English-only identifiers" (company standard)

❌ **EXCLUDE (standard BC):**
- "Use meaningful variable names" (generic advice)
- "Follow AL naming conventions" (standard BC)
- "Write unit tests" (standard practice)
- "Use SetLoadFields for performance" (base BC knowledge)

**Ask yourself:** "Does bc-code-intel's base layer already cover this, or is this an iFacto-specific rule?"

### Writing Style

**Voice:** Clear, direct, authoritative (these are company mandates)

**Format:**
- Use markdown headers for structure
- Use code fences with `al` language for AL code
- Use emoji sparingly for visual markers (✅ ❌)
- Use **bold** for emphasis on rules
- Use bullet lists for requirements

**Tone:**
- Professional but approachable
- Explain the "why" not just the "what"
- Assume audience is experienced BC developers
- Be explicit about iFacto-specific vs standard BC

### Code Examples Best Practices

1. **Show both correct and incorrect:**
   ```al
   // ✅ CORRECT
   var
       ErrorMsg: Label 'Error occurred';
   begin
       Error(ErrorMsg);
   end;

   // ❌ WRONG
   begin
       Error('Error occurred');  // Hardcoded text
   end;
   ```

2. **Use realistic scenarios** - Not "foo/bar" examples
3. **Include comments** - Explain why something is right/wrong
4. **Keep examples focused** - One concept per example
5. **Use complete code blocks** - Show full procedure/function context

## 🔧 Common Tasks & How to Handle Them

### Task: Add a New Guideline Topic

**Steps:**

1. **Identify the delta** - What's iFacto-specific about this guideline?
2. **Create topic file** - `company-bc-guidelines/topics/<topic-name>.md`
3. **🚨 ADD YAML FRONTMATTER FIRST** - This is MANDATORY for MCP indexing:
   ```yaml
   ---
   title: "Topic Title - Short Description"
   domain: "business-central"
   priority: critical|high|medium|low
   tags: ["tag1", "tag2", "tag3"]
   category: "code-quality|architecture|extensibility|project-structure"
   ---
   ```
4. **Write content** using the template structure above
5. **Add to knowledge-config.json:**
   ```json
   {
     "id": "topic-name",
     "title": "Display Title",
     "file": "topics/topic-name.md",
     "category": "appropriate-category",
     "keywords": ["relevant", "keywords", "here"],
     "priority": "high"
   }
   ```
6. **Update version** in knowledge-config.json (bump minor: 1.0.0 → 1.1.0)
7. **Validate YAML frontmatter** - Ensure all required fields are present
8. **Test** - Ask bc-code-intel questions using the keywords
9. **Update README.md** if needed (list of guidelines)

**✅ VALIDATION CHECKLIST BEFORE COMMITTING:**
- [ ] YAML frontmatter is present at the very top of the file
- [ ] All required YAML fields are included (title, domain, priority, tags, category)
- [ ] Domain is set to "business-central"
- [ ] Priority is one of: critical, high, medium, low
- [ ] Tags are relevant and searchable
- [ ] Category matches one of the valid categories
- [ ] knowledge-config.json has been updated with matching metadata
- [ ] Code examples show both ✅ CORRECT and ❌ WRONG patterns

**DO NOT automatically commit to git** - let the user decide when to commit.

### Task: Update an Existing Guideline

**Steps:**

1. **Read the current topic file** to understand existing content
2. **🚨 VERIFY YAML frontmatter exists** - If missing, add it immediately (CRITICAL for MCP)
3. **Make focused changes** - Don't rewrite everything unless necessary
4. **Update YAML frontmatter if needed:**
   - Title changed → Update both YAML and knowledge-config.json
   - Priority changed → Update both YAML and knowledge-config.json
   - Tags need adjustment → Update both YAML and knowledge-config.json
   - Category changed → Update both YAML and knowledge-config.json
5. **Update knowledge-config.json** only if YAML frontmatter changed
6. **Bump version** (patch for minor updates: 1.0.0 → 1.0.1)
7. **Check related guidelines** - Update cross-references if needed

**⚠️ IMPORTANT:** If an existing topic file is missing YAML frontmatter, this is a CRITICAL bug that must be fixed immediately. Without it, bc-code-intel cannot index the topic.

### Task: Review/Validate Repository Structure

**Checklist:**

- [ ] All topic files in `company-bc-guidelines/topics/`
- [ ] `knowledge-config.json` has entry for each topic
- [ ] All topic IDs in knowledge-config.json match filenames
- [ ] All file paths in knowledge-config.json are correct
- [ ] Keywords are relevant and searchable
- [ ] Version numbers follow semantic versioning
- [ ] No orphaned files (files not referenced in knowledge-config.json)
- [ ] No duplicate IDs in knowledge-config.json

## 🚫 Common Mistakes to Avoid

### 1. ❌ Forgetting YAML Frontmatter in Topic Files

**CRITICAL ERROR - BREAKS MCP INDEXING**

**WRONG:**
```markdown
# iFacto Error Handling Standards

**Company Standard - High Priority**
...
```

**RIGHT:**
```markdown
---
title: "Error Handling - Label Variables"
domain: "business-central"
priority: high
tags: ["error-handling", "label-variables", "company-standard"]
category: "code-quality"
---
# iFacto Error Handling Standards

**Company Standard - High Priority**
...
```

**Why this matters:** Without YAML frontmatter, bc-code-intel MCP server CANNOT:
- Index the topic for search
- Discover the topic when developers ask questions
- Apply the correct priority ranking
- Categorize the guideline properly

**This is the #1 mistake that breaks the entire MCP system.**

### 2. ❌ Writing AL Code in This Repository

**WRONG:**
```
"I'll create a sample Customer table extension to demonstrate..."
```

**RIGHT:**
```
"I'll document the guideline for Customer table extensions with a code example in the topic file..."
```

**Remember:** This repo documents guidelines; it doesn't build BC apps.

### 3. ❌ Including Standard BC Best Practices

**WRONG:**
```markdown
# Variable Naming

Variables should have meaningful names that describe their purpose.
```

**RIGHT:**
```markdown
# Naming Conventions - English-Only Identifiers

**iFacto Standard:** ALL identifiers (variables, procedures, fields) MUST be in English.
No Dutch or other languages in code. Use Caption property for translations.
```

### 4. ❌ Listing Specific Guidelines in Template/Documentation Files

**CRITICAL ERROR - UNDERMINES THE MCP SYSTEM**

**WRONG (in copilot-instructions-template.md or documentation files):**
```markdown
## Known iFacto-Specific Patterns

1. **Meth Codeunit Pattern** - Business logic separation with ONE public procedure
2. **Error Handling with Label Variables** - ALL Error()/Message()/Confirm() MUST use Label variables
3. **Code Organization** - Exactly ONE object per .al file
4. **English-Only Identifiers** - ALL identifiers must be English
5. **Enum Extensibility Rules** - Extensible = true ONLY when enum implements interface
```

**RIGHT:**
```markdown
## Using iFacto Company Standards

**ALWAYS query bc-code-intel MCP** to get iFacto company-specific guidance. The MCP server 
has access to all detailed topics - do NOT list specific patterns here.

Use: "Using iFacto company standards, [developer's question]"
```

**Why this matters:**
- Listing guidelines creates shortcuts that bypass the detailed topic content
- Copilot uses these short summaries instead of querying the full MCP topics
- When topics are updated, template files with hardcoded lists become outdated
- The topics/ folder is the SINGLE SOURCE OF TRUTH - everything else should reference it via MCP

**Where guidelines SHOULD appear:**
- ✅ `company-bc-guidelines/topics/*.md` - Full detailed guidelines (single source of truth)
- ✅ `company-bc-guidelines/knowledge-config.json` - Topic index for MCP discovery
- ✅ `company-bc-guidelines/README.md` - Brief summary with links to topics

**Where guidelines SHOULD NOT be duplicated:**
- ❌ `copilot-instructions-template.md` - Should explain HOW to query MCP, not WHAT the rules are
- ❌ `docs/*.md` - Documentation should reference topics, not duplicate them
- ❌ Root `README.md` - Should link to topics, not copy content

**This is a critical mistake that undermines the MCP system.** Topics are the single source of truth.

### 5. ❌ Creating Files Without Updating knowledge-config.json

**Always update knowledge-config.json when adding/removing topic files!**

bc-code-intel will NOT discover topics without metadata entries.

### 6. ❌ Using Vague Keywords

**WRONG:** `"keywords": ["stuff", "things", "code"]`

**RIGHT:** `"keywords": ["error", "label", "variable", "message", "exception"]`

Think: "What would a developer type when asking about this?"

### 7. ❌ Forgetting to Bump Version Numbers

Update `knowledge-config.json` version when making changes:
- **Major (1.0.0 → 2.0.0):** Breaking changes, removed guidelines
- **Minor (1.0.0 → 1.1.0):** New guidelines added
- **Patch (1.0.0 → 1.0.1):** Fixes, clarifications, typos

### 8. ❌ Auto-Committing to Git

**DO NOT run `git commit` commands automatically.** Create files and let the user review and commit when ready.

### 9. ❌ Referencing Topic Files from Agent Files

**CRITICAL ERROR - AGENTS SHOULD USE BC-CODE-INTEL SPECIALISTS, NOT DIRECT TOPIC REFERENCES**

**WRONG (in copilotfiles/agents/*.md):**
```markdown
## References & Resources

### iFacto Company Guidelines
- [Error Handling Standards](../../bc-company-guidelines/topics/ifacto-error-handling-standards.md)
- [Meth Codeunit Pattern](../../bc-company-guidelines/topics/ifacto-meth-codeunit-pattern.md)
- [Naming Conventions](../../bc-company-guidelines/topics/ifacto-naming-conventions.md)
```

**RIGHT:**
```markdown
## bc-code-intel Specialist Roles

### Company Standards (Consult First)
- **waldo** (`waldo-company`) - iFacto company-specific standards and patterns
  - **ALWAYS consult first** for company layer validation
  - Knows ALL iFacto topics: error handling, naming conventions, Meth patterns, enum rules, etc.

### BC Best Practices
- **Roger** (`roger-reviewer`) - General BC code quality and standards
- **Sam** (`sam-coder`) - AL coding patterns and implementation
```

**Why this matters:**
- Agents are COORDINATORS, not rule enforcers
- bc-code-intel specialists (like waldo) have access to ALL topics via the MCP server
- Direct topic links bypass the specialist system and create maintenance issues
- Specialists provide contextualized guidance, not just static topic content
- When topics are updated, agent files with hardcoded links become outdated

**Agent Architecture:**
- ✅ Agents coordinate and delegate to bc-code-intel specialists
- ✅ Specialists access topics through MCP server knowledge base
- ✅ Topics remain the single source of truth
- ✅ Changes to topics automatically flow to specialists without updating agents
- ❌ Agents should NEVER link directly to topic files

**Where topic references SHOULD appear:**
- ✅ `bc-company-guidelines/topics/*.md` - Within topics linking to related topics
- ✅ `bc-company-guidelines/README.md` - Summary with topic navigation

**Where topic references SHOULD NOT appear:**
- ❌ `copilotfiles/agents/*.md` - Agents use specialists, not topics directly
- ❌ `copilotfiles/prompts/*.md` - Prompts reference specialists, not topics

**This is critical for maintaining the proper separation between coordination (agents) and knowledge (specialists via MCP).**

## 🧪 Testing Your Changes

After creating or updating guidelines:

1. **Syntax check:** Ensure JSON is valid
   ```powershell
   Get-Content "company-bc-guidelines/knowledge-config.json" | ConvertFrom-Json
   ```

2. **Reference check:** All file paths in knowledge-config.json exist
   
3. **Conceptual test:** Ask yourself:
   - "Would bc-code-intel find this topic with these keywords?"
   - "Is this genuinely iFacto-specific or standard BC advice?"
   - "Are the code examples clear and correct?"

4. **User testing:** See TESTING-GUIDE.md for how developers test the company layer

## 📚 Key Reference Files

When working on this repository, frequently reference:

1. **`knowledge-config.json`** - The source of truth for topic metadata
2. **`DELTA-SUMMARY.md`** - Quick overview of what's iFacto-specific
3. **Existing topic files** - Maintain consistent format and quality
4. **TESTING-GUIDE.md** - Understand how this will be used

## 🎯 Success Criteria

Your work on this repository is successful when:

1. ✅ **Guidelines are delta-focused** - Only iFacto-specific rules
2. ✅ **MCP structure is correct** - knowledge-config.json properly maintained
3. ✅ **Code examples are clear** - BC developers can follow them
4. ✅ **Keywords are searchable** - bc-code-intel can find topics
5. ✅ **Documentation is complete** - Setup guides and references updated
6. ✅ **bc-code-intel can load it** - Server successfully indexes the company layer

## 💡 When in Doubt

**Ask yourself:**
1. "Is this an iFacto-specific rule or standard BC practice?"
2. "Would a BC developer naturally search for this with these keywords?"
3. "Are my code examples showing the RIGHT and WRONG way clearly?"
4. "Did I update knowledge-config.json?"

**Remember:**
- This is NOT a BC development project (no AL code compilation here)
- This is NOT for project-specific guidelines (that's per-project)
- This IS for company-wide iFacto standards that override base BC knowledge
- This IS meant to be consumed by bc-code-intel MCP server

---

## 🤝 Working with the User

- **Explain what you're doing** - Why you're creating/updating a particular file
- **Show the structure** - Help them understand the MCP knowledge layer
- **Suggest, don't automate** - Offer to create files, don't auto-commit them
- **Validate the delta** - Confirm rules are genuinely iFacto-specific

---

**Version:** 1.0  
**Last Updated:** October 14, 2025  
**Maintained for:** BCCompanyGuidelines repository - iFacto company layer for bc-code-intel MCP
