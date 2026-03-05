# Release & Exposure Playbook

This playbook defines the process for safely releasing a production-ready system from the Engineering Execution Kit to live production users. It governs the controlled introduction of validated software to production through defined exposure stages, monitored rollout, and explicit disposition.

---

## Artifact Flow

```
[Frozen ORD from EEK — Layer 4]
        │
        ▼
Step 0: Release Entry Gate
        │ Frozen RER
        ▼
Step 1: Release Context File (RCF)
        │ Frozen RCF (may reuse existing)
        ▼
Step 2: Release Plan (RP)
        │ Frozen RP + Pre-Release Authorization Checklist
        ▼
Step 3: Release Execution
        │ Human + tooling work; utility prompts support
        ▼
Step 4: Release Record (RR)
        │ Frozen RR
        ▼
[Handoff to Layer 6 — Reliability & Resilience]
```

---

## Upstream Interface

**What this kit receives:** A frozen Operational Readiness Document (ORD) from the Engineering Execution Kit (Layer 4).

The ORD confirms:
- The system's deployment infrastructure is verified and operational
- Rollback procedures are tested and documented in ORD §8
- Observability (logging, metrics, tracing) is in place
- All 8 ORD hard gates have passed

**What the ORD does not address:** How users will be exposed to the system, at what pace, under what authorization, or what constitutes success or failure at the organizational level. That is this kit's responsibility.

---

## Step 0: Release Entry Gate

**What:** Confirm the upstream ORD is frozen, the release scope is declared, a named release owner is accountable, and an explicit release authorization decision exists on record.

**Spec:** `docs/specs/release-entry-spec.md`
**Template:** `docs/artifacts/release-entry-template.md`
**Validator:** `docs/validators/release-entry-validator.md`
**Gate:** Validator PASS + human freeze
**Output:** Frozen Release Entry Record (RER)

### Steps

1. Complete `release-entry-template.md`:
   - Confirm the ORD ID and verify it is in Frozen status
   - Declare what is being released (system name, service, feature)
   - Select the release type: canary | blue-green | rolling | direct-full
   - State the initial exposure target
   - Name the release owner and their authorization level
   - Record the release authorization decision with a traceable reference

2. Run `release-entry-validator.md` against the completed record
3. Fix any blocking issues; re-run until PASS
4. Complete the Freeze Declaration
5. Proceed to Step 1 (or Step 2 if reusing an existing frozen RCF)

**Important:** ORD approval, sprint completion, and PR merge do not constitute release authorization. A separate, explicit release decision must be on record with a traceable reference.

---

## Step 1: Release Context File

**What:** Establish the organizational release standards that will govern this release. The RCF is an org-level, reusable artifact — it may be created once and reused across multiple releases until the organization's policy changes.

**Spec:** `docs/specs/rcf-spec.md`
**Template:** `docs/artifacts/rcf-template.md`
**Intake:** `docs/artifacts/release-context-intake-template.md`
**Prompt:** `docs/prompts/rcf-prompt.md`
**Validator:** `docs/validators/rcf-validator.md`
**Gate:** Validator PASS + human freeze
**Output:** Frozen Release Context File (RCF)

### Reusing an Existing RCF

If a frozen RCF already exists for the organizational scope covering this release:

1. Confirm the RCF ID and verify it is in Frozen status
2. Confirm its applicability scope covers this release
3. Reference the RCF ID in the Release Entry Record and in the Release Plan §1
4. Skip RCF generation — proceed directly to Step 2

**Do not modify a frozen RCF for a single release.** If the RCF needs a change, update it through the normal RCF authoring process (new version, re-validation, re-freeze) and apply it to future releases.

### Creating a New RCF

1. Complete `release-context-intake-template.md` with your organization's actual release standards
2. Run `rcf-prompt.md` with the completed intake form, release-safety-principles.md, and progressive-delivery-principles.md as inputs
3. Review the generated RCF for accuracy — especially authorization thresholds, tier criteria, and flag governance rules
4. Run `rcf-validator.md` against the RCF
5. Fix any blocking issues; re-run until PASS
6. Complete the Freeze Declaration (approved by a team or role with release policy authority)

---

## Step 2: Release Plan

**What:** Define the concrete, executable release strategy for this specific release. The RP translates ORD runbooks and RCF standards into deployment steps, exposure stages, rollback conditions, and monitoring protocols.

**Spec:** `docs/specs/rp-spec.md`
**Template:** `docs/artifacts/rp-template.md`
**Prompt:** `docs/prompts/rp-prompt.md`
**Validator:** `docs/validators/rp-validator.md`
**Gate:** Validator PASS + human freeze
**Output:** Frozen Release Plan (RP)

### Steps

1. Run `rp-prompt.md` with the frozen ORD, frozen RCF, and frozen RER as inputs
2. Review the generated RP — the release owner must confirm:
   - Deployment steps match actual ORD runbook procedures
   - Rollback conditions are observable and thresholds are realistic
   - Feature flag configuration is correct for each stage
   - Baseline metric values are sourced from ORD evidence
   - All RCF communication requirements are addressed
3. Run `rp-validator.md` against the RP
4. Fix any blocking issues; re-run until PASS
5. Complete the Freeze Declaration
6. Leave the Pre-Release Authorization Checklist (§8) blank — it is completed immediately before execution begins

**Optional:** Run `rollout-risk-assessment-prompt.md` against the frozen RP to identify execution risks before execution begins. This is advisory — findings must be assessed by the release owner.

---

## Step 3: Release Execution

**What:** Execute the release according to the frozen RP. This step is performed by humans using tooling; AI utility prompts support communication and documentation.

### Before Execution Begins

Complete the Pre-Release Authorization Checklist in RP §8:

- [ ] ORD status re-confirmed as Frozen (verify today — confirm it has not been amended)
- [ ] RP validated (PASS result on file) and frozen
- [ ] Feature flags configured per RP §3 for initial stage
- [ ] Rollback procedure reviewed and confirmed operational (ORD §8 runbook tested)
- [ ] Monitoring dashboards active; baseline values visible in monitoring system
- [ ] Automated alerts configured per RP §6; test-fire confirmed
- [ ] Pre-release internal communications sent per RP §5
- [ ] Release window confirmed with stakeholders
- [ ] Rollback responsible party confirmed available during release window
- [ ] Release owner authorizes execution to begin (name, date/time, sign-off)

Only proceed when all checklist items are confirmed. If any item cannot be confirmed, hold the release.

### During Execution

- Follow RP §2 deployment steps in order; do not skip or reorder steps
- After each stage, collect evidence: logs, metric values, deployment system output
- At each exposure stage, check signals against the RP §3 proceed trigger before expanding
- Record decisions and decision makers as execution progresses — the RR will need this
- If an alert fires, follow RP §4 rollback specification; do not wait beyond the decision window

### Abort Protocol

If the release must be abandoned (not rolled back to a prior state, but fully stopped):

1. Halt further exposure immediately
2. Document the state at abandonment: what was deployed, what exposure was reached
3. Perform cleanup per ORD §8 procedures
4. Record the abandonment decision: who made it, when, and why
5. Proceed to Step 4 — the RR must still be produced even for abandoned releases

### Re-Entry Protocol

If a change to the frozen RP is necessary after execution has begun (e.g., a rollback condition threshold must be adjusted mid-release):

1. **Stop execution** — do not proceed while the plan is contested
2. The release owner must document the change decision with rationale (date, time, who authorized)
3. Create a new RP version (RP-{PROJECT}-{NNN}-v2): copy the frozen RP, apply the change, re-validate, re-freeze
4. The Pre-Release Authorization Checklist must be re-completed for the new RP version
5. Resume execution under the new RP version
6. The RR must document both RP versions and the transition decision

---

## Step 4: Release Record

**What:** Structure the post-release execution evidence into the Release Record. The RR documents what actually happened — not what was planned. It is the formal declaration of release disposition and the handoff package to Layer 6.

**Spec:** `docs/specs/rr-spec.md`
**Template:** `docs/artifacts/rr-template.md`
**Prompt:** `docs/prompts/rr-prompt.md`
**Validator:** `docs/validators/rr-validator.md`
**Gate:** Validator PASS + human freeze
**Output:** Frozen Release Record (RR)

### Steps

1. Collect all execution evidence: deployment logs, metric observations, alert records, decision logs
2. Run `rr-prompt.md` with the frozen RP and collected evidence as inputs
3. Review the generated RR:
   - Confirm all RP deployment steps are documented with concrete evidence
   - Confirm all exposure stages are logged with observed metric values and named decision makers
   - Confirm the Handoff to Layer 6 section is complete
   - Confirm deviations from the RP are documented with rationale
4. Run `rr-validator.md` against the RR
5. Fix any blocking issues; re-run until PASS
6. Complete the Freeze Declaration

**Note:** The RP is not modified based on what happened. Deviations are documented in the RR only. The RP remains frozen as the plan of record.

---

## Downstream Handoff

**What Layer 6 receives:** The frozen RR, specifically §7 Handoff to Layer 6.

**RR §7 contains:**
- What is in production: system name, version or commit, deployed configuration
- Current exposure state: percentage, active flags and values
- Active monitoring: metric names, dashboard locations, alert configurations
- SLO baseline: pre-release values for error rate, latency, and key business metrics
- Open incidents at handoff (or "None")
- Recommended Layer 6 watch items: what aspects of the production system warrant elevated attention

The Layer 6 kit uses the RR §7 handoff section as its upstream boundary contract. Layer 6 oversight begins from the moment the RR is frozen and handed off.

---

## Freeze Points

| Artifact | Frozen By | Gate |
|----------|-----------|------|
| Release Entry Record | Release owner | Validator PASS |
| Release Context File | Team or role with release policy authority | Validator PASS |
| Release Plan | Release owner | Validator PASS |
| Release Record | Release owner | Validator PASS |

A frozen artifact is immutable. It may not be amended, revised, or silently updated after freeze. If a correction is required post-freeze:
- For the RER, RCF, or RP: create a new version with the change documented
- For the RR: post-release observations belong in Layer 6 records, not in amendments to the RR

---

## Session Discipline

**Generation and validation must run in separate AI sessions.** The same AI session that generates an artifact cannot validate it — this prevents self-validation bias.

**Standard session flow:**
1. Session A: Generate draft (run prompt)
2. Human review of draft
3. Session B: Validate draft (run validator)
4. Human reviews blocking issues
5. Session A (or C): Fix blocking issues and regenerate
6. Session B (or D): Re-validate
7. Human confirms PASS and freezes

**Never ask the generating session to check its own output against the spec.** The validator is the quality gate — use it.
