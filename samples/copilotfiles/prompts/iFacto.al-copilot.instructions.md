---
applyTo: '**/*.al'
---
# GitHub Copilot Instructions for AL (Business Central) Development

Keep this file small. Put workflow-specific guidance in on-demand skills, not in always-on instructions.

## Project Context File (Mandatory if exists)
After identifying the relevant workspace AL project boundary, check if `.github/copilot-context.md` exists in that project root.
If it exists, read it before proceeding - it contains project-specific architecture and patterns.

## AL Object Identification (mandatory in responses)
Always identify AL objects with full type + ID + name.

Examples
- Tables: `Table 38 "Purchase Header"`
- Pages: `Page 52 "Purchase Credit Memo"`
- Codeunits: `Codeunit 415 "Release Purchase Document"`
- Enums: `Enum 38 "Purchase Document Type"`
- Fields: `field(33; "Currency Factor"; Decimal)`

Symbol path structure
- Methods: `"Table 38 \"Purchase Header\"/InitRecord()"`
- Events: `"Table 38 \"Purchase Header\"/OnAfterInitRecord(...)"`
- Field references: `"Table 38 \"Purchase Header\"/fields/\"Currency Factor\": Decimal"`

## Keep Implementation Detail Out Of Always-On Context

For implementation-specific rules such as variable ordering, object IDs, affixes, access modifiers, and required field properties, consult **waldo** (`waldo-company`) via bc-code-intel — the company guidelines contain all iFacto-specific implementation standards.

## Disambiguation protocol (mandatory)
If the user references something ambiguous (field/method/table name exists in multiple objects), you MUST:

1. Use `al-mcp-server/al_find_references` or `grep_search` to search broadly.
2. If insufficient, use `ms-dynamics-smb.al/al_symbolsearch` for object-level filtering.
3. Present all plausible matches with object identity and file context.
4. Ask the user to choose.
5. Wait for confirmation before editing.

## Keep Detailed Workflows Out Of Always-On Context

For complex AL tasks, use the appropriate **iFacto agent** instead of the default agent or plan mode:

- Use **iFacto.ALCoder** for event subscriber signature matching, multiple extensions targeting the same base object, mirrored base-app implementation work, and any multi-object code generation.
- Use **iFacto.SolutionDesigner** for feature design, architecture decisions, and creating instruction documents before implementation.
- Use **iFacto.CodeReviewer** for findings-first code review against iFacto company standards and BC best practices.
- Use **iFacto.DevOps.PipelineAnalyzer** for pipeline failures, build errors, and deployment issues.
