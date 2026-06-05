---
applyTo: "**"
---
# Operational Monitoring

## LLM Cost Awareness

When using an expensive/premium model (Claude Opus, GPT-4.5, o3, or similar high-cost models), display a visible warning at the start of your response:

> ⚠️ **Expensive model active** — Consider switching to a cost-effective model (Claude Sonnet, GPT-4.1) unless this task requires advanced reasoning.

- Routine tasks (edits, navigation, formatting): recommend switching
- Complex tasks (architecture, multi-file refactoring, deep debugging): justified — still warn
- Agent explicitly specifies expensive model: intentional — note this

## Token Waste Monitoring

Self-monitor for inefficient patterns. Report via `ifacto-token-tracker/report_token_waste` when available; otherwise report inline once.

| Trigger | waste_type | severity |
|---------|-----------|----------|
| Session exceeds 15 turns without progress | `long_session` | medium/high |
| Asking user the same question again | `repetition` | medium |
| User prompt too vague to act on | `vague_prompt` | low |
| Conversation drifts from original task | `topic_drift` | medium |
| Failing to use an available tool | `not_using_tools` | high |
| Work clearly unrelated to iFacto | `off_topic_work` | high |

**Rules:**
- At session start, attempt to load `ifacto-token-tracker/report_token_waste` via `tool_search`
- If not found, note once: `⚠️ ifacto-token-tracker not available — waste reported inline only.`
- Report at most once per waste_type per session
- For `vague_prompt`: STOP before doing any work, ask for clarification
- For `off_topic_work`: report silently, never confront the user
- For non-blocking types: continue working after reporting

**Off-topic definition:** No apparent connection to Business Central, AL development, iFacto tooling, Azure DevOps pipelines for iFacto, or iFacto documentation. When in doubt, do NOT report.
