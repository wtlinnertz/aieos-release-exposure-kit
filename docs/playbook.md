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
Step 1a: Release Safety Assessment (RSA)
        │ Frozen RSA
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

## Step 1a: Release Safety Assessment

**What:** Aggregate risk evidence from upstream kits (QAK, SCK, EEK) into a structured safety brief. The RSA consolidates quality, security, and deployment risk signals into an actionable risk profile that informs the Release Plan.

**Spec:** `docs/specs/rsa-spec.md`
**Template:** `docs/artifacts/rsa-template.md`
**Prompt:** `docs/prompts/rsa-prompt.md`
**Validator:** `docs/validators/rsa-validator.md`
**Gate:** Validator PASS + human freeze
**Output:** Frozen Release Safety Assessment (RSA)

### Steps

1. Collect all available upstream risk evidence:
   - Frozen QGR from QAK (if adopted)
   - Frozen TM and SAR from SCK (if adopted)
   - Frozen ORD from EEK (for operational readiness evidence)
2. Run `rsa-prompt.md` with the frozen RER, frozen RCF, and all available upstream evidence as inputs
3. Review the generated RSA:
   - Confirm all upstream evidence is accurately cited (no invented analysis)
   - Confirm all four risk dimensions are covered in the Aggregate Risk Profile
   - Confirm safeguards are concrete and RP-implementable
   - Confirm residual risks have acceptance rationale
4. Run `rsa-validator.md` against the RSA
5. Fix any blocking issues; re-run until PASS
6. Complete the Freeze Declaration

**Note:** The RSA does not perform its own quality, security, or risk analysis. It aggregates findings from upstream kits. If QAK or SCK are not adopted for this initiative, the RSA documents the absence of that evidence and relies on ORD for available quality/security signals.

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

1. Run `rp-prompt.md` with the frozen ORD, frozen RCF, frozen RER, and frozen RSA as inputs
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
| Release Safety Assessment | Release owner or risk delegate | Validator PASS |
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

---

## Amendment Procedure

A frozen artifact may be corrected in place without re-validation when **all** of the following criteria are met:

1. The correction does not affect any field evaluated by a hard gate.
2. The correction does not change scope, decisions, owners, or technical specifications.
3. The correction does not affect any field referenced by a downstream artifact.

**Procedure:** Make the correction and add an Amendment Log entry to the artifact's Document Control section: date, what changed, materiality criterion cited, and who authorized the change. No re-validation is required.

**If there is any ambiguity** about whether a change is non-material, treat it as material and create a new artifact version. The amendment path must not become a workaround for the version protocol.

---

## Escalation Paths

This kit may be the source of escalations to upstream kits when a rollback reveals that the issue is not a release execution problem but an upstream defect.

### Trigger 3 — Release Rollback Caused by Code Defect (to EEK)

**Signal:** A release has been rolled back and the root cause is a code or build defect in the system delivered by the Engineering Execution Kit.

**What to do:**
1. Complete the Release Record (RR) documenting the rollback. The RR §3 release disposition must capture: rollback executed, rollback reason, and root cause category (code defect).
2. Create an escalation record documenting: trigger type (Trigger 3), triggering RR ID, defect description, and recommended action (EEK re-entry for defect fix).
3. Send the escalation record to the EEK team. They will create a Kit Entry Record and handle the fix.
4. Hold this release in rolled-back state pending the EEK fix and a new ORD. Do not attempt to re-release the original system.
5. When the EEK delivers a new ORD for the fixed system, open a new Release Entry Record and begin a new release cycle.

**A human must authorize** the escalation before sending. The release owner reviews the RR root cause assessment and confirms the defect is an EEK issue before escalating.

### Trigger 4 — Release Rollback Revealing Wrong Feature (to PIK)

**Signal:** A release has been rolled back and the assessment reveals the feature should not have been built as specified — the problem is product direction, not execution quality.

**What to do:**
1. Complete the Release Record documenting the rollback. The RR §3 release disposition must capture: rollback executed, rollback reason, and root cause category (product direction mismatch).
2. Create an escalation record documenting: trigger type (Trigger 4), triggering RR ID, description of the product direction problem (what was built vs. what users/business actually needed), and recommended action (PIK discovery restart).
3. Send the escalation record to both the EEK team and the PIK team. EEK needs to know the DPRD may be revisited. PIK needs the escalation record as discovery intake context.
4. The PIK team assesses whether a new discovery engagement is warranted. This is their decision — the REK team does not dictate the PIK response.
5. This feature will not be re-released until PIK and EEK have completed a new cycle.

**A human must authorize** the Trigger 4 escalation. The decision that a rollback is a "wrong feature" rather than a "wrong execution" is a significant product judgment — it requires the release owner and a product stakeholder to agree.

---

## Principle File Revision

When a principle file in `docs/principles/` changes, use the change categories defined in `aieos-governance-foundation/docs/principle-file-standard.md`:

| Change Category | Version Bump | Re-Entry Impact |
|----------------|-------------|-----------------|
| **Minor** (clarification only) | `v_.x → v_.x+1` | No re-entry required; already-frozen artifacts remain valid |
| **Significant** (new requirement or tightened constraint) | `v1.x → v1.x+1` | Review artifacts generated after the change against updated principles; already-frozen artifacts are grandfathered |
| **Breaking** (removal or loosening) | `vN.x → vN+1.0` | Requires service owner authorization and documented business justification; re-entry may be warranted |

Every change to a principle file must bump the version field, even minor clarifications.

---

## Deprecation and Sunset

When a release engagement ends — either by completing the release, being cancelled mid-flow, or the system being decommissioned — the artifacts it produced transition to terminal lifecycle states. See the full protocol in `aieos-governance-foundation/docs/deprecation-protocol.md`.

### When to Deprecate or Abandon

| Situation | Action |
|-----------|--------|
| Release completed; RR frozen and handed off to RRK | No immediate action — artifacts become Deprecated when the system reaches end of life |
| System decommissioned after releases are complete | `Deprecated` on all frozen REK artifacts for that system (RER, RP, RR, and any applicable RCF versions) |
| Release cancelled after one or more artifacts are Frozen | `Deprecated` on frozen artifacts; `Abandoned` on any non-frozen artifacts |
| Release cancelled before any artifact is Frozen | `Abandoned` on all in-progress artifacts |

### Who Authorizes

A release owner, engineering lead, or equivalent role must authorize the terminal state transition. Do not move to `Deprecated` or `Abandoned` without an authorizing name and role recorded in the Deprecation Notice.

### How to Issue a Deprecation Notice (DN)

1. Confirm the cancellation or conclusion decision is authorized.
2. List all artifacts in the release series (RER, RCF, RP, RR) with their current status.
3. For each artifact: if Frozen → `Deprecated`; if not Frozen → `Abandoned`.
4. Create a DN record at `docs/sdlc/dn-{project-id}-{NNN}.md` using the format in `aieos-governance-foundation/docs/deprecation-protocol.md`.
5. Update each artifact's Status field to its terminal state (non-material amendment; add Amendment Log entry per governance model §6).

### What Does Not Change

- Artifacts are retained — never deleted.
- The RCF is shared across releases — deprecating one release's artifacts does not deprecate the RCF unless the RCF's entire scope is decommissioned.
- Terminal state does not require re-validation.
- If the release is rescheduled or reattempted, new artifacts with new IDs are produced.

---

## Maintaining the Engagement Record

The Engagement Record (ER) is a project-level artifact that lives in the consuming project at `docs/engagement/er-{initiative}.md`. It spans all AIEOS layers and is maintained by each kit's operators as work passes through. The ER spec and format are defined in `aieos-governance-foundation/docs/engagement-record-spec.md`.

**REK maintains the Layer 5 section of the ER.**

### What to Update During Release

| Trigger | ER update |
|---------|-----------|
| RER frozen | Add RER ID to §4 artifact table |
| RCF version confirmed | Add RCF ID and version to §4 |
| RSA frozen | Add RSA ID to §4 artifact table |
| RP frozen | Add RP ID to §4 |
| RR frozen | Add RR ID to §4; record release disposition in §4 (Released / Rolled Back / Abandoned) |
| Significant decision made | Add entry to §4 Key Decisions — release type justification, deviations from RP during execution, gate failures or validator issues |

If the release is rolled back or abandoned, add a brief cause note in §4 alongside the disposition.

### On Service End

When issuing a Deprecation Notice: update §1 Status if appropriate and add the DN ID to §7 Initiative Outcome.
