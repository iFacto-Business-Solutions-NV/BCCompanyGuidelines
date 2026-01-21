---
agent: iFacto.Documentation
---
Create comprehensive pull request documentation by automatically gathering information from available sources and generating all 5 mandatory components: JIRA integration, code changes overview, testing documentation, functional setup guide, and architecture review with honest star ratings.

## 🎯 BC Code Intelligence Specialist Integration

**MANDATORY: Use taylor-docs specialist from bc-code-intel MCP**

Before generating any PR documentation, consult with the **taylor-docs** specialist:
- **Purpose:** taylor-docs specializes in Business Central documentation standards, PR documentation best practices, and technical writing for BC projects
- **When to use:** At the START of PR documentation generation to ensure proper structure, standards compliance, and BC-specific documentation patterns
- **How to invoke:** Use `mcp_bc-code-intel_ask_bc_expert` tool with `preferred_specialist: "taylor-docs"`
- **What to ask:** "Help me create comprehensive pull request documentation for [brief PR description]. Analyze the changes and ensure documentation follows BC and iFacto standards."

**taylor-docs provides:**
- BC-specific documentation patterns and standards
- PR documentation structure guidance
- Technical writing best practices for AL/BC code
- Quality assurance for documentation completeness
- Company-specific documentation standards enforcement

## � JIRA MCP Server Integration

**MANDATORY: Use mcp-server-atlassian-jira MCP for ALL JIRA interactions**

All JIRA issue retrieval, validation, and requirement extraction MUST use the mcp-server-atlassian-jira MCP server:

- **Purpose:** Direct integration with Atlassian JIRA to fetch real issue data
- **When to use:** EVERY time you need JIRA information - validation, requirements, issue details
- **MCP Server:** `mcp-server-atlassian-jira`
- **Critical Operations:**
  - Verify JIRA issue exists
  - Fetch issue summary, description, and details
  - Retrieve acceptance criteria and requirements
  - Get comments, subtasks, and related issues
  - Extract priority, labels, status, and assignee information

**NEVER:**
- ❌ Simulate JIRA data
- ❌ Create fake issue responses
- ❌ Proceed without actual JIRA MCP connection
- ❌ Use placeholder JIRA information

**If mcp-server-atlassian-jira is not available:**
- Stop immediately and inform the user
- Explain that JIRA MCP server is required for PR documentation
- Ask user to configure mcp-server-atlassian-jira before proceeding
**ERROR MESSAGE FORMAT when MCP is unavailable:**
```
❌ JIRA MCP SERVER NOT AVAILABLE

The mcp-server-atlassian-jira MCP server is required to create PR documentation but is not currently available or configured.

**What's needed:**
- mcp-server-atlassian-jira MCP server must be installed and configured
- Connection to Atlassian JIRA must be established
- JIRA issue validation is MANDATORY for PR documentation

**Cannot proceed with:**
- ❌ Simulated or placeholder JIRA data
- ❌ Manual JIRA information entry
- ❌ Documentation generation without JIRA validation

**Action required:**
Please configure mcp-server-atlassian-jira MCP server before creating PR documentation.
```
## �🚨 CRITICAL REQUIREMENT - JIRA Issue

**MANDATORY JIRA ISSUE NUMBER REQUIRED:**
- A JIRA issue number is ABSOLUTELY REQUIRED to proceed with PR documentation
- If no JIRA issue is provided in the user's request or context, you MUST ask for it
- NEVER create simulated, placeholder, or fake JIRA issue references
- NEVER proceed with documentation generation without a real JIRA issue number
- The format should be: PROJECT-#### (e.g., DEV-123, CRM-456, etc.)

**Stop and ask the user:** "I need a JIRA issue number to create the PR documentation. Please provide the JIRA issue number for this pull request (format: PROJECT-####)."

### JIRA Issue Validation (MANDATORY FIRST STEP)

**Once JIRA issue number is provided:**

1. **Verify Issue Exists** - Use **mcp-server-atlassian-jira MCP** to validate the issue exists
   - Use the `jira_get_issue` tool from mcp-server-atlassian-jira MCP
   - Pass the issue number provided by the user (e.g., ISSUE-123)
   - **This call MUST succeed** - If it fails, stop immediately
   - If issue doesn't exist: Stop and show ERROR message
   - If MCP connection fails: Stop and show ERROR message
   - If call succeeds: Store the following context for later verification:
     - Issue key and summary
     - Issue description (full text)
     - Acceptance criteria (if present)
     - All requirements and user stories
     - Comments with additional context
     - Status, priority, and labels
     - Assignee and reporter information

**CRITICAL: The jira_get_issue call is NON-NEGOTIABLE**
```
Required MCP Tool: jira_get_issue
Required Parameter: issue_key (e.g., "ISSUE-123")
Expected Response: Complete JIRA issue object with all fields
On Failure: STOP - Show error and do NOT proceed
On Success: Store complete issue context for requirement validation
```

**ERROR MESSAGE FORMAT when issue validation fails:**
```
❌ JIRA ISSUE VALIDATION FAILED

Issue: [ISSUE-NUMBER]
Status: [NOT FOUND / MCP CONNECTION FAILED]

**Problem:**
[Choose appropriate message:]
- The JIRA issue [ISSUE-NUMBER] does not exist in the system
- Cannot connect to mcp-server-atlassian-jira MCP server
- mcp-server-atlassian-jira is not configured or not available

**Cannot proceed with:**
- ❌ PR documentation generation
- ❌ Requirement validation
- ❌ Any documentation work

**Action required:**
[Choose appropriate action:]
- Verify the JIRA issue number is correct
- Configure mcp-server-atlassian-jira MCP server
- Ensure JIRA connection is established
```

2. **Fetch Issue Requirements** - Use **mcp-server-atlassian-jira MCP** `jira_get_issue` to retrieve:
   - Issue description and summary (from jira_get_issue response)
   - Acceptance criteria (if defined in description or custom fields)
   - Business requirements and user stories (from description)
   - Priority and labels (from jira_get_issue response)
   - Comments with additional context (from jira_get_issue response)
   - **Store ALL retrieved data** for later requirement compliance validation

3. **Store JIRA Context for Verification** - Save the following for later use:
   ```
   JIRA Context to Remember:
   - Issue Key: [From jira_get_issue]
   - Summary: [From jira_get_issue]
   - Description: [Full text from jira_get_issue]
   - Acceptance Criteria: [Extracted from description]
   - Requirements: [All requirements listed in description/comments]
   - Status: [From jira_get_issue]
   - Priority: [From jira_get_issue]
   ```
   This context will be used to validate PR changes against requirements.

4. **Compare PR Changes to Requirements** - Analyze if PR changes address the JIRA requirements:
   - ✅ List requirements that ARE met by the PR changes
   - ⚠️ List requirements that are PARTIALLY met (need clarification)
   - ❌ List requirements that are NOT met by the PR changes
   - Flag any PR changes that don't map to JIRA requirements (scope creep)

4. **Requirement Compliance Check** - Include in final documentation:
   ```markdown
   ## JIRA Requirement Compliance
   
   ### Requirements Met ✅
   - [Requirement 1 from JIRA]: [How PR addresses this]
   - [Requirement 2 from JIRA]: [How PR addresses this]
   
   ### Requirements Partially Met ⚠️
   - [Requirement]: [What's implemented vs. what's missing]
   
   ### Requirements Not Met ❌
   - [Requirement]: [Why not addressed - out of scope, future work, etc.]
   
   ### Out-of-Scope Changes
   - [Change in PR]: [Justification if not in JIRA requirements]
   ```

## Pull Request Change Detection

**Automatically detect and focus ONLY on pull request changes:**

### 1. Git-Aware Change Analysis
- **Detect uncommitted changes** - Modified files in working directory that will be committed
- **Identify branch-specific commits** - Changes committed since branch was created from master/main/base branch
- **Analyze only PR-relevant files** - Skip files unchanged since branch creation
- **Focus on AL objects** - Tables, pages, codeunits, reports, enums created or modified for this PR

### 2. JIRA Issue Details
- **Validate issue exists** - Use **mcp-server-atlassian-jira MCP** to confirm issue is exists
- **Fetch comprehensive details** - Use **mcp-server-atlassian-jira MCP** to retrieve:
  - Issue description and summary
  - Acceptance criteria and business requirements
  - Comments and additional context
  - Priority, labels, and stakeholders
- **Compare to PR changes** - Validate PR addresses JIRA requirements
- **Flag discrepancies** - Identify requirements not met or out-of-scope changes

### 3. Code Changes Analysis (PR-Focused)
- Analyze git commits and diffs to understand what files were changed in THIS branch
- Compare current state with branch base to identify ALL modifications for the PR
- Examine code patterns and architectural decisions made in the changed files
- Identify new files, modified files, and deleted files specific to this pull request

### 4. Testing Evidence Discovery
- Scan for new or modified test files in commits specific to this PR
- **CRITICAL: If NO test files found, clearly flag this as missing automated tests**
- Identify test procedures and test scenarios added in branch commits
- Check for manual testing documentation in PR-specific commit messages
- Look for edge cases covered in test implementations added to this branch
- Analyze test coverage and regression test updates for PR changes
- **NEVER fabricate or invent test content when no actual tests exist**

### 5. Technical Implementation Context
- Review PR-specific code changes to understand technical approach
- Identify design patterns, events, and architectural decisions made in this branch
- Assess integration points and dependencies affected by PR changes
- Understand configuration and setup requirements introduced in this PR

### 6. Impact Assessment
- Analyze systems and components affected by PR changes
- Identify performance implications of modifications in this branch
- Review security and permission considerations for changed files
- Assess extensibility and maintainability aspects of PR modifications

## Required Input from User

**MANDATORY REQUIREMENTS - DO NOT PROCEED WITHOUT THESE:**

- **JIRA Issue Number**: [REQUIRED - MUST be provided by user - NEVER simulate or create fake issues]
  - Ask explicitly if not provided: "Please provide the JIRA issue number for this PR"
  - Must be real issue number in format PROJECT-#### (e.g., DEV-123, CRM-456)
  - Use JIRA MCP server to validate and fetch issue details

**Only ask for additional items if cannot be automatically determined:**

- **Special Setup Requirements**: [Only if not evident from PR code changes]
- **Manual Testing Performed**: [Only if not documented in PR commits or tests]
- **Known Limitations**: [Only if not apparent from PR code review]

## Git-Aware Documentation Generation

**PREREQUISITE: JIRA Validation (MUST COMPLETE BEFORE DOCUMENTATION GENERATION)**

Before starting ANY PR documentation work, you MUST:

1. **Connect to mcp-server-atlassian-jira** - Establish connection to JIRA MCP server
   - Use **mcp-server-atlassian-jira MCP** for all JIRA operations
   - **Use the `jira_get_issue` tool** with the issue number provided by user
   - If connection fails: Stop and inform user that mcp-server-atlassian-jira is not available or not configured
   - Request JIRA issue number if not already provided

2. **Verify Issue Exists** - Use **mcp-server-atlassian-jira MCP** `jira_get_issue` to fetch the issue
   - Call: `jira_get_issue(issue_key="ISSUE-123")` (or user-provided issue number)
   - **This call MUST succeed** - Do NOT proceed if it fails
   - Query JIRA for the provided issue number
   - If issue doesn't exist: Stop and inform user
   - If issue exists: **Store complete response for requirement validation**

3. **Extract All JIRA Requirements** - Get complete issue details from `jira_get_issue` response:
   - Issue summary and description (parse from response)
   - Acceptance criteria (if defined in description or fields)
   - Business requirements and user stories (extract from description)
   - All comments and additional context (from response)
   - Subtasks and related issues (from response links)
   - Priority, labels, and status (from response fields)
   - **REMEMBER THIS CONTEXT** - It will be used to validate PR changes meet requirements

4. **Analyze PR Changes** - Review what was actually implemented:
   - Get list of all files changed in this PR
   - Analyze code modifications and additions
   - Identify what functionality was implemented

5. **Compare Requirements vs. Implementation** - Critical validation step:
   - **Use the stored JIRA context from jira_get_issue response**
   - Compare each requirement from JIRA to actual PR code changes
   - ✅ Identify JIRA requirements that ARE fully addressed by PR changes
   - ⚠️ Identify JIRA requirements that are PARTIALLY addressed (incomplete)
   - ❌ Identify JIRA requirements that are NOT addressed at all (missing)
   - 🔍 Identify PR changes that DON'T map to JIRA requirements (scope creep)
   - **Document the comparison** - Show which JIRA requirements map to which PR changes

6. **Requirement Compliance Report** - Present findings to user:
   ```markdown
   ## JIRA Requirement Validation Results
   
   **JIRA Issue:** [ISSUE-123] - [Issue Summary]
   
   ### ✅ Requirements Fully Implemented
   - [Requirement 1]: [What was implemented]
   - [Requirement 2]: [What was implemented]
   
   ### ⚠️ Requirements Partially Implemented
   - [Requirement 3]: [What's done] | [What's missing]
   
   ### ❌ Requirements NOT Implemented
   - [Requirement 4]: [Why not addressed]
   
   ### 🔍 Out-of-Scope Changes (Not in JIRA)
   - [PR Change]: [Justification needed]
   
   **Action Required:** 
   - If requirements are missing: Should they be implemented before creating PR?
   - If out-of-scope changes exist: Should they be removed or documented?
   ```

7. **Get User Confirmation** - Do NOT proceed until user confirms:
   - "The JIRA requirements analysis is complete. Do you want to proceed with PR documentation generation, or do you need to make additional code changes first?"

**ONLY AFTER USER CONFIRMATION, proceed to documentation generation steps below.**

---

**STEP 1: Consult taylor-docs specialist**
After JIRA validation is complete and user confirms, use `mcp_bc-code-intel_ask_bc_expert` with `preferred_specialist: "taylor-docs"` to:
- Get guidance on BC documentation standards for this specific PR
- Ensure documentation structure follows best practices
- Validate that all required sections will be properly addressed

**STEP 2: Generate PR description content (NOT a separate file)**
After consulting taylor-docs, generate the PR description text by analyzing ONLY the changes in this pull request.

**CRITICAL: DO NOT create a separate PR-DOCUMENTATION.md file!**
**CRITICAL: Use the documentation content DIRECTLY in the PR description when creating the PR!**
**CRITICAL: Keep descriptions CONCISE - Azure DevOps has length limits!**
**CRITICAL: Use ONLY headings, lists, and emoticons - NO code blocks, NO tables!**
**CRITICAL: NEVER EXCEED 3800 CHARACTERS - Count characters before creating PR!**

Generate the following content structure for the PR description (simplified format):

```markdown
# [Emoticon] [JIRA-ISSUE]: [Title]

## [Section with emoticon]
- Bullet points only
- No code blocks
- No tables
- Keep concise

## Summary
Brief paragraph summarizing the change

## Changes
- List of changes
- Organized by category
- With emoticons for visual clarity

## Testing
- Manual testing results
- List format only
- Include success indicators

## Deployment
- Prerequisites
- Steps
- Configuration

## Assessment
- Architecture ratings in list format
- Overall recommendation
- Risk level

---

Commit info
```

**FORMAT RESTRICTIONS:**
- ✅ Headings (# ## ###)
- ✅ Bullet lists (- or numbered)
- ✅ Emoticons (⚠️ ✅ ⭐ 🔧 📋 etc.)
- ✅ Bold text
- ❌ NO code blocks or backticks
- ❌ NO tables with pipes
- ❌ **CRITICAL: NEVER exceed 3800 characters total length**
- ❌ NO complex formatting

**⚠️ CHARACTER LIMIT WARNING:**
- **HARD LIMIT: 3800 characters maximum**
- Azure DevOps will truncate content beyond this limit
- Always count characters before creating PR
- Prioritize essential information if approaching limit
- Use concise language and abbreviations when necessary

**IMPLEMENTATION - CREATE PR WITH AZURE REST API:**

**CRITICAL: ALWAYS use Azure REST API via az rest - NEVER use az repos pr create/update**

The `az repos pr` commands have a known bug with multi-line text. Use the REST API method from the start:

```powershell
# Step 1: Generate description content (CRITICAL: keep under 3800 characters)
$description = @"
# PR Title

## Section 1
Content here

## Section 2
More content
"@

# Step 2: Create JSON body file
$title = "Your PR Title"
$sourceBranch = "refs/heads/your-branch"
$targetBranch = "refs/heads/master"

$prBody = @{
    sourceRefName = $sourceBranch
    targetRefName = $targetBranch
    title = $title
    description = $description
} | ConvertTo-Json -Depth 10

$prBody | Out-File -FilePath "pr-create.json" -Encoding UTF8

# Step 3: Create PR using Azure REST API
$prResult = az rest --method POST `
  --uri "https://dev.azure.com/{org}/{project}/_apis/git/repositories/{repoId}/pullrequests?api-version=7.1" `
  --body "@pr-create.json" `
  --headers "Content-Type=application/json" `
  --resource "499b84ac-1321-427f-aa17-267ca6975798" | ConvertFrom-Json

# Step 4: Clean up
Remove-Item "pr-create.json"

# Get PR ID for reference
$prId = $prResult.pullRequestId
Write-Host "PR Created: #$prId"
```

## Intelligent Analysis Approach

### BC Specialist Consultation (FIRST STEP)
```
1. Invoke taylor-docs specialist via bc-code-intel MCP
2. Provide PR context and change summary
3. Get documentation structure and standards guidance
4. Apply BC-specific best practices to documentation
5. Ensure iFacto company standards compliance
```

### Git Commit Analysis (PR-Focused)
```
1. Get list of commits in current branch/PR since branch creation
2. Analyze git diff for each PR-specific commit
3. Categorize PR changes by file type and impact
4. Extract PR commit messages for context
5. Identify patterns and architectural decisions made in this PR
```

### Code Quality Assessment (PR Changes Only)
```
1. Analyze AL code patterns and best practices compliance in changed files
2. Check naming conventions and structure for PR modifications
3. Assess error handling and validation in PR changes
4. Review performance implications of PR modifications
5. Evaluate extensibility patterns in PR code
```

### Test Coverage Analysis (PR Tests)
```
1. Identify new test codeunits and procedures added in PR
2. Map PR tests to functionality being tested
3. Assess edge case coverage for PR changes
4. Review regression test completeness for PR impact
5. Validate test data and scenarios for PR functionality
```

### Business Context Integration
```
1. Validate JIRA issue exists via mcp-server-atlassian-jira (CRITICAL FIRST STEP)
2. Fetch Jira issue details via mcp-server-atlassian-jira MCP
3. Extract business requirements and acceptance criteria
4. Compare PR changes to JIRA requirements - identify gaps
5. Map technical implementation to business needs
6. Identify user impact and value delivered
7. Assess priority and urgency context
8. Flag any requirements NOT met by PR changes
```

## Success Criteria

This intelligent prompt succeeds when it:
- ✅ Consults taylor-docs specialist via bc-code-intel MCP before generating documentation
- ✅ Applies BC-specific documentation standards and best practices from taylor-docs
- ✅ Generates 90%+ of documentation content automatically from PR changes only
- ✅ Requires minimal user input (only JIRA issue number typically)
- ✅ Provides accurate technical analysis based on actual PR code changes
- ✅ Includes honest, realistic architecture assessments of PR modifications
- ✅ Delivers professional-quality documentation ready for PR review
- ✅ Saves significant time while improving PR documentation quality
- ✅ Focuses exclusively on changes that will be included in the pull request

## Example Usage

**REQUIRED user input - NEVER proceed without this:**
```
JIRA Issue: DEV-456

[AI then automatically handles the rest by:]
- Validating JIRA issue exists via mcp-server-atlassian-jira MCP
- Fetching JIRA issue requirements and acceptance criteria via mcp-server-atlassian-jira MCP
- Consulting taylor-docs specialist via bc-code-intel MCP for documentation guidance
- Analyzing git commits and code changes specific to this PR
- Comparing PR changes to JIRA requirements - identifying gaps
- Identifying test coverage and scenarios for PR changes
- Assessing architectural quality and patterns in PR modifications
- Applying BC documentation standards from taylor-docs
- Generating comprehensive documentation with requirement compliance check
```

**If user doesn't provide JIRA issue:**
```
User: "Create PR documentation for my changes"
AI Response: "I need a JIRA issue number to create the PR documentation. Please provide the JIRA issue number for this pull request (format: PROJECT-####)."
```

**Result:** Complete, professional PR documentation with all 5 mandatory components, generated automatically with minimal human input, focusing exclusively on pull request changes.

## 🔍 Pre-Documentation Validation

**Before starting ANY documentation generation, validate:**

1. **JIRA Issue Check**: Is a real JIRA issue number provided?
   - ❌ NO JIRA Issue = Stop and ask for it
   - ❌ Simulated/Placeholder Issue = Stop and ask for real one
   - ✅ Real JIRA Issue (PROJECT-####) = Proceed with validation

1a. **JIRA Issue Validation** (via mcp-server-atlassian-jira MCP):
   - ❌ Issue doesn't exist = Stop and inform user
   - ❌ MCP connection failed = Stop and inform user that mcp-server-atlassian-jira is required
   - ✅ Issue exists and retrieved = Proceed with requirement analysis

1b. **JIRA Requirement Analysis** (via mcp-server-atlassian-jira MCP):
   - Extract acceptance criteria and requirements from JIRA
   - Compare PR changes to JIRA requirements
   - Flag any gaps or discrepancies
   - Identify out-of-scope changes

2. **Test Coverage Check**: Are there actual automated tests in the PR?
   - ❌ NO Tests Found = Use warning format in Testing Documentation section
   - ❌ NEVER invent or fabricate test content when none exists
   - ✅ Real Tests Found = Document actual test procedures and coverage

3. **Never Create Fake Content**: 
   - NEVER use text like "Simulated - actual JIRA integration would be via MCP server"
   - NEVER create placeholder content like "DEV-2024-ESC (Simulated)"
   - NEVER fabricate test procedures, frameworks, or coverage when none exist
   - ALWAYS use **mcp-server-atlassian-jira MCP** for real JIRA integration when JIRA issue is provided
   - ALWAYS accurately represent testing state (including lack of tests)
   - If mcp-server-atlassian-jira is not available, STOP and inform user - do NOT proceed with fake data



*This intelligent prompt leverages available tools and context to automate PR documentation generation, requiring minimal user input while delivering maximum value and accuracy by focusing only on pull request changes.*

Generate complete pull request documentation that includes ALL of the following mandatory sections:

### 1. JIRA Issue Integration
- Link directly to the JIRA issue (format: [ISSUE-123](https://ifacto.atlassian.net/browse/ISSUE-123))
- Provide brief issue summary if not clear from title
- Ensure business context is clearly established

### 2. Code Changes Overview
Create a comprehensive overview with:
- **What changed:** List all modified files with specific changes
- **Why it changed:** Clear business rationale and technical necessity
- **How it was implemented:** Key architectural decisions and patterns used

Example format:
- **What changed:**
  - Customer table: Added validation triggers for email format
  - CustomerValidationMgt codeunit: Centralized validation logic
  - Customer Card page: Added validation message FactBox
- **Why:** Reduce invalid customer data entry by 80% (business requirement)
- **How:** Event-driven validation architecture for extensibility

### 3. Testing Documentation
Document ALL testing performed:
- **Automated Tests:** List new/updated test codeunits and procedures
  - **If no automated tests exist:** Use warning format: "⚠️ **WARNING: No Automated Tests Detected**"
- **Manual Testing:** Step-by-step verification procedures
- **Edge Cases:** Specific boundary conditions and error scenarios tested
- **Regression Testing:** Confirmation existing functionality still works

Include actionable test steps that others can follow to verify the changes.
**CRITICAL:** Never fabricate test content when no actual tests exist - show warning instead.

### 4. Functional Setup Guide
Provide complete setup instructions:
- **Prerequisites:** What needs to be configured before use
- **Setup Steps:** Step-by-step configuration instructions
- **Usage Instructions:** How end users interact with the functionality
- **Configuration Options:** Available settings and their impact

Write for someone unfamiliar with the feature who needs to set it up.

### 5. Architecture Review with Star Ratings
Provide honest assessment using 1-5 star scale (use list format for PR descriptions):

**Architecture Assessment:**
- **Code Architecture:** ⭐⭐⭐⭐ - Clear separation, minor coupling issues
- **Code Quality:** ⭐⭐⭐⭐⭐ - Well-documented, excellent error handling
- **Performance:** ⭐⭐⭐⭐ - Good optimization, some potential bottlenecks
- **Maintainability:** ⭐⭐⭐⭐⭐ - Clear structure, easy to extend
- **Extensibility:** ⭐⭐⭐⭐ - Good event usage, some hardcoded elements

**Rating Scale:**
- ⭐ - Poor (needs significant improvement)
- ⭐⭐ - Below Average (has issues that should be addressed)
- ⭐⭐⭐ - Average (acceptable but could be better)
- ⭐⭐⭐⭐ - Good (solid implementation with minor improvements needed)
- ⭐⭐⭐⭐⭐ - Excellent (exemplary implementation, follows all best practices)

Be honest - not everything deserves 5 stars. Identify specific areas for improvement.

## Input Requirements

When using this prompt, provide:
- **JIRA Issue:** Issue number and brief description
- **Files Changed:** List of modified files with change descriptions
- **Business Context:** Why this change was needed
- **Technical Approach:** Key implementation decisions made
- **Testing Performed:** Summary of testing completed
- **Special Considerations:** Any unique aspects of this change

## Output Format

**For PR Descriptions (following STEP 2 format restrictions):**
Use the simplified format shown in STEP 2 above with:
- Headings and bullet lists only
- Emoticons for visual clarity
- No code blocks or tables
- **CRITICAL: Under 3800 characters total length (HARD LIMIT)**

**For Comprehensive Documentation (if creating separate documentation):**
Use detailed format with full sections, tables, and code examples as needed.

## Quality Standards

**For PR Descriptions (STEP 2 format):**
- ✅ Follows format restrictions (no tables, no code blocks)
- ✅ **CRITICAL: Under 3800 characters (verify before creating PR)**
- ✅ Uses emoticons and bullet lists only
- ✅ Links to actual JIRA issue with proper URL
- ✅ Provides concise but actionable information

**For Comprehensive Documentation (if separate file):**
- ✅ Has NO placeholder text or TBD entries
- ✅ Links to actual JIRA issue with proper URL
- ✅ Provides actionable testing instructions
- ✅ Includes honest architecture assessment (not all 5-star ratings)
- ✅ Contains sufficient detail for code review and future maintenance
- ✅ Can be understood by both technical and non-technical reviewers
- ✅ **Accurately represents testing state - shows WARNING when no automated tests exist**
- ✅ **NEVER fabricates test content, procedures, or coverage when none exists**

## Example Usage

To use this prompt effectively:

1. **Gather Information First:**
   ```
   JIRA Issue: CRM-456 - Implement customer email validation
   Files Changed: Customer table, CustomerValidationMgt codeunit, Customer Card page
   Business Context: Reduce data quality issues and improve customer communication
   Technical Approach: Event-driven validation with extensibility points
   Testing: Created automated tests, tested edge cases, verified existing workflows
   ```

2. **Apply This Prompt:**
   ```
   Using the information above, create comprehensive PR documentation following 
   all 5 mandatory sections with proper formatting and honest assessments.
   ```

3. **Review and Refine:**
   - Verify all sections are complete
   - Ensure ratings are realistic
   - Confirm setup instructions are actionable
   - Validate links work correctly

## Success Criteria

This prompt succeeds when it generates documentation that:
- Passes organizational review standards
- Provides complete context for reviewers
- Enables future developers to understand changes
- Includes actionable setup and testing instructions
- Gives honest assessment of code quality and architecture

---

*This prompt is designed to generate high-quality PR documentation efficiently while ensuring compliance with organizational standards and reviewer expectations.*