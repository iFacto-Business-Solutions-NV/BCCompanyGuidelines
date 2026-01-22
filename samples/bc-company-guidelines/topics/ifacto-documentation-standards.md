---
id: "company/ifacto-documentation-standards"
title: "iFacto Documentation Standards for BC Projects"
domain: "company-standards"
tags: ["documentation", "changelog", "user-guides", "setup-docs", "test-docs", "mandatory", "ifacto"]
difficulty: "intermediate"
bc_version: "14+"
estimated_time: "30 minutes"
priority: "high"
status: "mandatory"
added: "December 2024"
---

# iFacto Documentation Standards for BC Projects

**Company Standard - High Priority**

## What Makes This Company-Specific

Standard BC development doesn't mandate specific documentation structures. **iFacto requires** a standardized `Docs` folder structure with specific file organization, changelog format, and cross-referencing patterns for all BC projects.

## The Standard: Structured Documentation in `Docs` Folder

### Mandatory Folder Structure

```
Docs/
├── README.md              (REQUIRED - Navigation hub)
├── CHANGELOG.md          (REQUIRED - Weekly grouped changes)
├── Users/                (End-user documentation)
├── Setup/                (Configuration guides)
├── Tests/                (Manual testing procedures)
└── Dev/                  (Developer reference)
    └── Dependencies/     (Per-dependency documentation)
```

## Core Requirements

### 1. **README.md - Navigation Hub** (REQUIRED)

**Purpose**: Single entry point with clickable links to all documentation.

**Must contain**:
- Brief app overview and purpose
- Clickable links to all documentation folders
- Quick navigation to frequently accessed documents
- Documentation structure explanation

**Example structure**:
```markdown
# App Name - Documentation

## Overview
Brief description of the app's purpose.

## Documentation Structure

### 📚 [User Guides](Users/)
- [Feature A](Users/Feature-A.md)
- [Feature B](Users/Feature-B.md)

### ⚙️ [Setup Documentation](Setup/)
- [Feature A Setup](Setup/Feature-A-Setup.md)

### 🧪 [Test Documentation](Tests/)
- [Feature A Tests](Tests/Feature-A-Tests.md)

### 📋 [Changelog](CHANGELOG.md)
Complete history of all changes.
```

### 2. **CHANGELOG.md - Weekly Grouped History** (REQUIRED)

**Format**: ISO week grouping (YYYY.WW) with JIRA-linked entries.

**Rules**:
- Group entries by ISO week number (newest first)
- Each JIRA issue key as clickable markdown link
- One line per entry (single sentence summary)
- Link to related documentation when it exists
- Group commits by JIRA issue AND person

**Example**:
```markdown
## 2025.47
- [BEG-123](https://ifacto.atlassian.net/browse/BEG-123) – Added VMF item exclusivity feature [See: Setup/VMF-Exclusivity-Setup.md](Setup/VMF-Exclusivity-Setup.md)
- [BEG-124](https://ifacto.atlassian.net/browse/BEG-124) – Updated delivery calendar calculations

## 2025.46
- [BEG-120](https://ifacto.atlassian.net/browse/BEG-120) – Fixed warehouse scanning validation
```

### 3. **Users/ - End-User Documentation**

**Audience**: Business Central end-users (NOT developers).

**Focus on**:
- What changed or was added by this app
- How users perform daily tasks with these changes
- Typical business workflows

**Avoid**:
- Technical jargon (AL code, table IDs, events)
- Standard BC behavior (unless contrasting with customization)
- Developer implementation details

**Structure**:
```markdown
# Feature Name

## Purpose
What this feature does for the user.

## When to Use
Business situations where this is relevant.

## How to Use
1. Step-by-step user instructions
2. Focus on pages, actions, fields

## Notes & Limitations
Important caveats or special cases.
```

### 4. **Setup/ - Configuration Guides**

**Audience**: Consultants and administrators.

**Focus on**:
- How to configure custom features
- Setup pages and key fields
- Configuration examples
- Prerequisites and dependencies

**Structure**:
```markdown
# Setup – Feature Name

## Prerequisites
Required master data, permissions, other setups.

## Configuration Steps
1. Navigate to setup page
2. Fill in key fields
3. Recommended values

## Verification
How to verify configuration is correct.

## Related Documentation
- User Guide: [Feature Name](../Users/Feature-Name.md)
- Testing: [Feature Tests](../Tests/Feature-Tests.md)
```

### 5. **Tests/ - Manual Testing Procedures**

**Audience**: Functional consultants and QA testers (NOT developers).

**CRITICAL**: Document **manual functional testing**, NOT automated test code.

**Never reference**:
- Test codeunits or AL procedures
- Automated test frameworks
- Technical test implementation

**Focus on**:
- Business scenarios to validate
- UI-based test procedures
- Step-by-step manual testing
- Expected business results

**Structure**:
```markdown
# Tests – Functional Area

## Test Overview
Business functionality being validated.

## Prerequisites
- Setup configuration needed
- Master data requirements
- User permissions

## Test Scenarios

### Test 1: Business Scenario Name
**Test Purpose**: Business situation this validates

**Preconditions**:
- Master data setup
- Configuration settings

**Test Steps**:
1. Navigate to [Page Name]
2. Enter/select values in fields
3. Click action/button
4. Verify results

**Expected Results**:
- What user should observe
- Business rules enforced
- Error messages (if applicable)

## Related Documentation
- User Guide: [Feature](../Users/Feature.md)
- Setup: [Configuration](../Setup/Feature-Setup.md)
```

### 6. **Dev/ - Developer Reference**

**Audience**: Developers working on this app.

**Must contain**:
- `README.md` as entry point for developer docs
- `Dependencies/` subdirectory with one file per dependency

**Dependencies Documentation**:
- File naming: `Dependency-[Name].md`
- Document WHY dependency is needed
- Document WHERE it's used in codebase
- Include version and publisher info
- Explain impact of removal/changes

## Cross-Referencing Rules (MANDATORY)

**ALWAYS use clickable markdown links** for all cross-references:

**Format**:
```markdown
[Display Text](relative/path/to/file.md)
```

**Examples**:
```markdown
- Setup docs from Users/: [Setup Guide](../Setup/Feature-Setup.md)
- User docs from Setup/: [User Guide](../Users/Feature.md)
- JIRA issues: [BEG-123](https://ifacto.atlassian.net/browse/BEG-123)
```

**Never use**:
- Plain filenames: "See file.md" ❌
- Non-clickable references: "file.md" ❌
- Absolute paths ❌

## JIRA Integration (When Available)

When JIRA MCP tools are available:
1. Detect JIRA issue references in commits
2. Retrieve issue details (summary, description, status)
3. Enrich changelog entries with issue summaries
4. Link issue keys in all documentation
5. Validate issue existence before documenting

## Documentation Workflow

### When Creating Documentation

1. **Analyze git history**:
   - Review ALL commits (not just recent)
   - Extract commit messages, authors, dates
   - Detect JIRA issue references

2. **Cross-reference with existing docs**:
   - Identify missing changelog entries
   - Find undocumented features
   - Detect outdated information

3. **Verify against code**:
   - Read actual implementation
   - Verify field names, procedures, workflows
   - Ensure accuracy (never assume)

4. **Create/update all documentation**:
   - Update CHANGELOG.md
   - Create/update Users/ docs
   - Create/update Setup/ docs
   - Create/update Tests/ docs
   - Update README.md navigation

5. **Cross-reference everything**:
   - Link changelog entries to documentation
   - Connect user guides to setup docs
   - Reference test procedures from setup guides

## Why This Matters (iFacto Perspective)

- **Consistency**: All BC projects documented the same way
- **Findability**: README hub makes documentation discoverable
- **Traceability**: CHANGELOG tracks all changes chronologically
- **Usability**: Clear separation (users vs. consultants vs. developers)
- **Maintainability**: Structured format makes updates easier
- **Quality**: Code verification ensures accuracy
- **Collaboration**: JIRA integration connects work to issues

## Enforcement

When reviewing BC project documentation:
- ✅ Check `Docs` folder exists with required structure
- ✅ Verify README.md has navigation links
- ✅ Validate CHANGELOG.md uses weekly grouping
- ✅ Ensure JIRA keys are clickable links
- ✅ Confirm cross-references use markdown links
- ✅ Verify Tests/ documents manual procedures (not code)
- ✅ Check accuracy against actual code implementation

## Common Mistakes to Avoid

❌ **Wrong**: Plain filename references
```markdown
See Setup/Feature-Setup.md for configuration
```

✅ **Correct**: Clickable markdown links
```markdown
See [Feature Setup](Setup/Feature-Setup.md) for configuration
```

❌ **Wrong**: Test documentation referencing AL code
```markdown
Tests are in codeunit 50100 "Feature Tests"
```

✅ **Correct**: Manual functional test procedures
```markdown
### Test 1: Validate Feature Behavior
1. Navigate to Feature Setup page
2. Enable feature toggle
3. Verify feature works in Feature Card page
```

❌ **Wrong**: CHANGELOG with plain JIRA references
```markdown
- BEG-123 – Added feature
```

✅ **Correct**: CHANGELOG with clickable JIRA links
```markdown
- [BEG-123](https://ifacto.atlassian.net/browse/BEG-123) – Added feature [See: Setup/Feature-Setup.md](Setup/Feature-Setup.md)
```

## Related Guidelines

- Use with error handling standards for consistent error documentation
- Combine with naming conventions for clear documentation file names
- Apply code organization rules when documenting code structure

---

**Remember**: Documentation is not optional. Every BC project must have complete, accurate, structured documentation in the `Docs` folder following these standards. When in doubt about documentation completeness, consult the bc-code-intel Taylor specialist for guidance.
