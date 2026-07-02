---
name: iFacto.al-security-review
description: 'Review AL code for BC-specific security risks (SecretText, IsolatedStorage, permissions, DataClassification, HttpClient, injection, telemetry, startup hooks). Has three modes — design (proactive threat model), code (inline guidance while writing), review (pass/fail audit of finished code). Use from iFacto.SolutionDesigner, iFacto.ALCoder, and iFacto.CodeReviewer.'
---

# AL Security Review

## When to Use

Invoke this skill whenever the work touches a security-sensitive AL primitive — credentials, permissions, web services, PII, telemetry — or whenever a security-focused review is requested.

Trigger constructs include: `SecretText`, `IsolatedStorage`, `HttpClient`, `permissionset`, `entitlement`, `InherentPermissions`, `InherentEntitlements`, `DataClassification`, `SetFilter` with user input, `Session.LogMessage`, `FeatureTelemetry`, `OnCompanyOpen`, install/upgrade codeunits, API pages, SOAP/OData exposure.

## Standards Source

All three modes evaluate against:
[../../../bc-company-guidelines/topics/ifacto-security-standards.md](../../../bc-company-guidelines/topics/ifacto-security-standards.md)

The seven categories: (1) secrets & credentials, (2) permissions & entitlements, (3) data classification, (4) external communication, (5) injection & filter safety, (6) telemetry & logging hygiene, (7) run-as elevation & startup hooks.

## Modes

Pick the mode that matches the calling context. The mechanics are similar — what differs is *when* you run, what you consult, and how you format the output.

### Mode: `design` (used by iFacto.SolutionDesigner)

**Goal:** identify security concerns BEFORE code exists, and codify them as constraints in the instruction document.

1. Read the feature description / JIRA / draft design
2. For each of the 7 security categories in `ifacto-security-standards.md`, decide: applicable or not?
3. For each applicable category, consult `waldo-company` via `mcp_bc-code-intel_ask_bc_expert`:
   > "Feature: <summary>. Reference category <N> of ifacto-security-standards.md. List the design constraints that must appear in the instruction document so the implementation will pass review."
4. (Optional) consult `alex-architect` for higher-level architectural impact (auth model, trust boundaries, data flow)
5. Output a **"Security Considerations" section** ready to paste into `App/Docs/Instructions/<KEY>.design.md`

**Output format (design mode):**
```markdown
## Security Considerations

### Applicable categories
- [x] 1. Secrets & credentials — feature stores API token
- [ ] 2. Permissions & entitlements — N/A (no new objects)
- [x] 3. Data classification — new fields capture customer email
- [x] 4. External communication — calls vendor REST API
- [ ] 5. Injection & filter safety — N/A
- [x] 6. Telemetry & logging — feature emits import telemetry
- [ ] 7. Run-as elevation & startup hooks — N/A

### Design constraints
1. API token MUST be `SecretText` stored via `IsolatedStorage` with `DataScope::Module` (waldo)
2. New `Loyalty Email` field MUST use `DataClassification = EndUserIdentifiableInformation` (waldo)
3. All HTTP calls MUST use HTTPS; no certificate validation bypass (waldo)
4. Telemetry MUST exclude email/token; use business keys only (waldo)

### Open questions
- Which scope for the token: Module (single tenant config) or Company (per-company)?
```

### Mode: `code` (used by iFacto.ALCoder)

**Goal:** make sure the right AL primitives are used WHILE the code is being written — not after.

1. Identify which of the 7 categories the current task touches (look at the design doc's Security Considerations section if present; otherwise infer from the task)
2. For each applicable category, consult `waldo-company`:
   > "I'm about to write <object/feature>. Show the iFacto-compliant AL pattern for category <N>, with the exact AL primitives I must use."
3. Receive concrete code patterns (e.g., the `SecretText` + `IsolatedStorage.Get` pattern, the `DataClassification` value to pick, the `Permissions` entry to add)
4. Apply patterns as you write
5. After implementation, run `iFacto.al-build-validation` (diagnostics-only) — security-relevant rules in CodeCop / AppSourceCop / PerTenantExtensionCop become evidence

**Output format (code mode):** inline guidance as you implement — no standalone report. State which patterns you applied:
```
🔒 Applied iFacto security patterns:
- Category 1 (secrets): API key stored via IsolatedStorage.SetEncrypted, DataScope::Module
- Category 3 (data classification): "Loyalty Email" = EndUserIdentifiableInformation
- Category 4 (HTTP): HTTPS only, SecretText for Authorization header
```

### Mode: `review` (used by iFacto.CodeReviewer)

**Goal:** validate finished code against iFacto security standards. Produce a pass/fail report. Critical findings block merge.

1. Determine review scope (changed files on current branch, or user-supplied file list)
2. Follow `iFacto.al-build-validation` skill in diagnostics-only mode to collect CodeCop / AppSourceCop / PerTenantExtensionCop output (some security issues are flagged by rulesets)
3. Consult `waldo-company` via `mcp_bc-code-intel_ask_bc_expert`:
   > "Validate the following AL code against `ifacto-security-standards.md`. For each of the 7 categories, provide ✅ PASS or ❌ FAIL with specific line references and exact corrections. Categorize findings as Critical / High / Advisory."
4. Consult `roger-reviewer` for general BC security patterns not yet codified as iFacto rules
5. Consolidate into the report below

**Output format (review mode):**
```markdown
## 🔒 AL Security Review

### Files reviewed
- {list}

### Per-category verdict (waldo-company)
- ✅/❌ 1. Secrets & credentials: {detail with line refs}
- ✅/❌ 2. Permissions & entitlements: {detail}
- ✅/❌ 3. Data classification: {detail}
- ✅/❌ 4. External communication: {detail}
- ✅/❌ 5. Injection & filter safety: {detail}
- ✅/❌ 6. Telemetry & logging hygiene: {detail}
- ✅/❌ 7. Run-as elevation & startup hooks: {detail}

### Compiler / ruleset findings
- CodeCop / AppSourceCop / PerTenantExtensionCop security-relevant warnings: {count + list}

### General BC security (roger-reviewer)
- {findings outside the 7 iFacto categories}

### 🚨 Critical (block merge)
- {all Critical findings}

### ⚠️ High (fix before release)
- {all High findings}

### 💡 Advisory
- {all Advisory findings}

### Verdict
Security: ✅ APPROVED / ❌ CHANGES REQUIRED
```

## Important

- **This skill is NOT a replacement for waldo/specialist consultation** — it runs alongside it, focused on security
- **In `review` mode, Critical findings are blocking** — same severity as company guideline violations
- **In `design` mode, output is markdown to insert into the instruction doc** — not a standalone deliverable
- **In `code` mode, output is inline guidance** — the skill influences the code being written, no separate report
- **Always reference the topic file** — never invent security rules ad hoc; cite the category number and section
- **Hand off if scope is wrong** — if a "security review" actually needs a full code review, defer to the agent's waldo + Roger consultation and only contribute the security categories
