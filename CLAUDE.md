# CLAUDE.md — Release & Exposure Kit

## What This Repository Is

This is the **Release & Exposure Kit** — an AIEOS kit that governs the safe introduction of production-ready systems to users. It is Layer 5 of the AIEOS system. It receives a frozen ORD from the Engineering Execution Kit and governs deployment strategy, progressive exposure, rollback conditions, and post-release monitoring.

## Repository Structure

```
docs/
  principles/          # Organizational release policy (input material)
  specs/               # Content rules and hard gates per artifact type
  artifacts/           # Templates and intake forms
  prompts/             # AI generation + utility prompts
  validators/          # Quality gate definitions
  playbook.md          # End-to-end process definition
  index.md             # Documentation entry point
  how-to-adapt.md      # Organizational adoption guidance
  how-to-use-with-ai.md # AI tool usage guide
  governance-model.md  # AIEOS structural rules (reference)
examples/              # Worked example (TaskFlow Notifications release)
tests/                 # Structural integrity checks
```

## Artifact Types

This kit produces three governed artifact types in sequence:

1. **Release Context File (RCF)** — Organizational release policy (deployment standards, exposure standards, authorization model, communication requirements, monitoring requirements). Reusable across multiple releases within its scope.
2. **Release Plan (RP)** — Initiative-specific release plan (deployment strategy, exposure stages, rollback conditions, communication plan, monitoring protocol).
3. **Release Record (RR)** — Post-release evidence artifact documenting what happened (deployment evidence, exposure log, monitoring observations, final disposition, Layer 6 handoff).

Each artifact type has exactly four governing files: spec, template, prompt, validator.

Plus one entry gate:

- **Release Entry Record (RER)** — Step 0 gate (human-authored). Confirms the ORD is frozen, declares release scope, names the release owner, confirms authorization exists. Validated against `release-entry-spec.md` before release planning begins.

## Utility Prompts

Three utility prompts support the flow but do not produce governed artifacts:

- **Rollout Risk Assessment** (`rollout-risk-assessment-prompt.md`) — Adversarial analysis of the Release Plan before execution begins
- **Release Communication** (`release-communication-prompt.md`) — Generates draft communications for internal stakeholders and customers from the Release Plan
- **ORD Acceptance Check** (`ord-acceptance-check-prompt.md`) — Cross-boundary consistency check verifying the RER accurately reflects the frozen ORD: production readiness, monitoring systems, and open items from ORD §N. Run after RER draft and before the Release Entry Validator.

## Key Rules

- **Specs are the source of truth** — prompts and validators reference specs, never inline rules
- **Validators judge, they do not help** — no suggestions, no redesign
- **Freeze before promote** — ORD must be frozen before release planning begins; RER and RCF must be frozen before RP generation; RP must be frozen and Pre-Release Authorization Checklist completed before execution begins
- **Separate generation and validation** — different AI sessions to prevent self-validation bias
- **No scope expansion** — RP scope must match RER declaration; RR must reflect RP exactly (deviations documented, not silently changed)
- **No inferred information** — mark missing information explicitly, do not fill gaps
- **Governance model sync** — `docs/governance-model.md` is a synchronized copy of `aieos-governance-foundation/governance-model.md` (canonical authority). Do not edit kit copy directly; update `aieos-governance-foundation` first, then sync all kit copies to match exactly. See governance-model.md §15 for versioning and change protocol.
- **Engagement Record** — REK maintains the Layer 5 section of the project's ER. Add artifact IDs as they freeze and record the release disposition. See `docs/playbook.md §Maintaining the Engagement Record` and `aieos-governance-foundation/docs/engagement-record-spec.md`.

## Artifact Flow

```
Step 0: Release Entry Record → validate → freeze → authorize release planning
Step 1: Release Context File (or reuse existing frozen RCF)
        → generate from intake → validate → freeze
Step 2: Release Plan → generate from ORD + RCF → validate → freeze
        → complete Pre-Release Authorization Checklist → begin execution
[Release Execution — human + tooling; AI utility prompts support]
Step 3: Release Record → generate from evidence + RP → validate → freeze
        → handoff to Layer 6
```

## Boundary Contracts

- **Upstream:** Receives a frozen ORD from the Engineering Execution Kit (Layer 4). The ORD must have passed all 8 EEK hard gates and be in Frozen status. Confirmed via Release Entry Record §2. See `docs/entry-from-eek.md` for the boundary briefing.
- **Downstream:** Produces a frozen Release Record that Layer 6 (Reliability & Resilience) uses as input. The RR handoff section (§7) explicitly documents what is in production, what monitoring is active, and the SLO baseline.

## File Naming

| Type | Pattern |
|------|---------|
| Spec | `{type}-spec.md` |
| Template | `{type}-template.md` |
| Prompt | `{type}-prompt.md` |
| Validator | `{type}-validator.md` |
| Utility Prompt | `{function}-prompt.md` |
| Intake Form | `{context}-intake-template.md` |

## When Working on This Kit

- Read the playbook (`docs/playbook.md`) for the full process definition
- Read the governance model (`docs/governance-model.md`) for structural rules
- Check `docs/how-to-use-with-ai.md` for session setup instructions
- Use `docs/session-setup.md` for per-artifact setup checklists and pre-flight gate checks
- Use `docs/troubleshooting.md` when a validator returns FAIL — maps each gate failure to a specific remediation
- Reference `examples/basic-release/` for a complete worked example

## Building or Auditing AIEOS Kits

- `aieos-governance-foundation/docs/kit-structure-standard.md` — compliance checklist for building and auditing kits
- `aieos-governance-foundation/docs/philosophy.md` — design rationale for governance model decisions
- `aieos-governance-foundation/docs/layer-model.md` — seven-layer model and kit registry
