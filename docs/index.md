# Release & Exposure Kit — Documentation Index

This kit governs Layer 5 of the AIEOS system: how production-ready systems are shipped safely to users.

---

## Start Here

| Document | Purpose |
|----------|---------|
| `playbook.md` | **Complete process definition** — read this first |
| `how-to-use-with-ai.md` | Session setup for AI-assisted generation and validation |
| `how-to-adapt.md` | Organizational adoption and customization guidance |
| `governance-model.md` | AIEOS structural rules (reference) |

---

## Artifact Types

| Artifact | Spec | Template | Prompt | Validator |
|----------|------|----------|--------|-----------|
| Release Entry Record (RER) | `specs/release-entry-spec.md` | `artifacts/release-entry-template.md` | — | `validators/release-entry-validator.md` |
| Release Context File (RCF) | `specs/rcf-spec.md` | `artifacts/rcf-template.md` | `prompts/rcf-prompt.md` | `validators/rcf-validator.md` |
| Release Plan (RP) | `specs/rp-spec.md` | `artifacts/rp-template.md` | `prompts/rp-prompt.md` | `validators/rp-validator.md` |
| Release Record (RR) | `specs/rr-spec.md` | `artifacts/rr-template.md` | `prompts/rr-prompt.md` | `validators/rr-validator.md` |

---

## Utility Prompts

| Prompt | Purpose |
|--------|---------|
| `prompts/rollout-risk-assessment-prompt.md` | Adversarial analysis of Release Plan before execution |
| `prompts/release-communication-prompt.md` | Draft release communications from Release Plan |
| `prompts/ord-acceptance-check-prompt.md` | Cross-boundary check: verify RER accurately reflects ORD production readiness, monitoring systems, and open items |

---

## Intake Forms

| Form | Purpose |
|------|---------|
| `artifacts/release-entry-template.md` | Release Entry Record (human-authored entry gate) |
| `artifacts/release-context-intake-template.md` | Human input for Release Context File generation |

---

## Principles

| File | Scope |
|------|-------|
| `principles/release-safety-principles.md` | Deployment safety, rollback requirements, authorization model |
| `principles/progressive-delivery-principles.md` | Feature flag governance, rollout standards, exposure lifecycle |

---

## Examples

| Path | What it shows |
|------|---------------|
| `examples/basic-release/` | Complete release flow for the TaskFlow Notifications feature — all 4 artifacts with PASS validator outputs |

---

## Guides

| Document | Purpose |
|----------|---------|
| `session-setup.md` | Per-artifact setup checklists, pre-flight gate checks, and common failure reminders |
| `troubleshooting.md` | Gate failure remediation guide |
| `entry-from-eek.md` | Boundary briefing when arriving from the Engineering Execution Kit |

---

## Tests

| File | Contents |
|------|----------|
| `tests/kit-test-plan.md` | Structural integrity checks (S-01 to S-08) + flow scenarios (F-00 to F-02) |
