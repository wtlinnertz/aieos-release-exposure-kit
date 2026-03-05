# Release & Exposure Kit — Test Plan

This document defines structural integrity checks and flow scenario tests for the Release & Exposure Kit.

## How to Use This Test Plan

Each check or scenario describes what to verify and what constitutes a pass. Structural checks can be run by inspection. Flow scenarios are walk-throughs of complete or partial release flows.

---

## Part 1: Structural Integrity Checks

These checks verify internal consistency — no broken references, no contradictions between documents.

### S-01: File Reference Integrity

**What:** Every file reference in prompts, validators, specs, playbook, and READMEs points to a file that exists.

**How:** Scan all `.md` files for references to other `.md` files. Verify each referenced file exists in the repository.

**Files to scan:** All files in `docs/prompts/`, `docs/validators/`, `docs/specs/`, `docs/artifacts/`, `docs/playbook.md`, `docs/index.md`, `docs/how-to-adapt.md`, `docs/how-to-use-with-ai.md`

**Pass criteria:** Zero broken file references.

---

### S-02: Four-File Completeness

**What:** Every governed artifact type has all required files.

**How:** For each artifact type, confirm all expected files exist.

**Expected files:**

| Type | Spec | Template | Prompt | Validator |
|------|------|----------|--------|-----------|
| Release Context File | `rcf-spec.md` | `rcf-template.md` | `rcf-prompt.md` | `rcf-validator.md` |
| Release Plan | `rp-spec.md` | `rp-template.md` | `rp-prompt.md` | `rp-validator.md` |
| Release Record | `rr-spec.md` | `rr-template.md` | `rr-prompt.md` | `rr-validator.md` |

**Entry gate files (spec + template + validator — no prompt, human-authored):**

| Type | Spec | Template | Validator |
|------|------|----------|-----------|
| Release Entry Record | `release-entry-spec.md` | `release-entry-template.md` | `release-entry-validator.md` |

**Utility prompts (no spec, template, or validator — advisory output):**

| Prompt | File |
|--------|------|
| Rollout Risk Assessment | `rollout-risk-assessment-prompt.md` |
| Release Communication | `release-communication-prompt.md` |

**Pass criteria:** All expected files exist. No artifact type is missing a required file.

---

### S-03: Hard Gate Alignment

**What:** Hard gates listed in each spec match the hard gates evaluated by the corresponding validator.

**How:** For each artifact type, extract the hard gate list from `{type}-spec.md` (the `## Hard Gates` section) and compare it to the gate keys in `{type}-validator.md` (the `"hard_gates"` object in the JSON output format). They must match exactly — same names, same count.

**Expected alignment:**

| Artifact | Spec Hard Gates | Validator Gate Keys |
|----------|----------------|---------------------|
| RER | document_control, upstream_reference, release_scope, release_owner, priority_confirmed | document_control, upstream_reference, release_scope, release_owner, priority_confirmed |
| RCF | document_control, deployment_standards, exposure_standards, authorization_model, communication_requirements, monitoring_requirements, standard_enforceability | document_control, deployment_standards, exposure_standards, authorization_model, communication_requirements, monitoring_requirements, standard_enforceability |
| RP | upstream_references, deployment_specification, exposure_specification, rollback_specification, communication_plan, monitoring_plan, scope_bounded, readiness | upstream_references, deployment_specification, exposure_specification, rollback_specification, communication_plan, monitoring_plan, scope_bounded, readiness |
| RR | release_summary, deployment_evidence, exposure_log, monitoring_observations, release_disposition, handoff_declaration | release_summary, deployment_evidence, exposure_log, monitoring_observations, release_disposition, handoff_declaration |

**Pass criteria:** Every spec's hard gate list matches its validator's gate keys exactly (same names, same count).

---

### S-04: Template Section Alignment

**What:** Template sections match the spec's required sections.

**How:** For each artifact type, compare the `## Required Sections` list in `{type}-spec.md` to the top-level sections (`## N. ...`) in `{type}-template.md`.

**Expected alignment:**

| Artifact | Spec Required Sections | Template Sections |
|----------|----------------------|------------------|
| RER | Document Control, Upstream Verification, Release Scope, Release Owner, Priority Confirmation, Completeness Checklist, Freeze Declaration | Same |
| RCF | Document Control, Deployment Standards, Exposure Standards, Release Authorization Model, Communication Requirements, Monitoring Requirements, Scope and Exceptions | Same (+ Freeze Declaration) |
| RP | Document Control, Deployment Specification, Exposure Specification, Rollback Specification, Communication Plan, Post-Release Monitoring Plan, Open Issues, Pre-Release Authorization Checklist | Same (+ Freeze Declaration) |
| RR | Document Control, Release Summary, Deployment Evidence, Exposure Log, Monitoring Observations, Release Disposition, Handoff to Layer 6 | Same (+ Freeze Declaration) |

**Pass criteria:** All spec-required sections are present in the template. Template may add supplementary elements (Freeze Declaration is a structural convention, not a spec-defined section — its presence in templates is expected).

---

### S-05: Prompt-Spec Reference Consistency

**What:** Generation prompts reference the correct spec and inputs.

**How:** For each generation prompt (rcf-prompt.md, rp-prompt.md, rr-prompt.md), verify:
- The prompt references the correct `{type}-spec.md` in its SPEC REFERENCE section
- The INPUTS section lists the correct upstream artifacts

**Expected:**

| Prompt | Spec Reference | Required Inputs |
|--------|---------------|-----------------|
| rcf-prompt.md | rcf-spec.md | release-context-intake-template.md, release-safety-principles.md, progressive-delivery-principles.md |
| rp-prompt.md | rp-spec.md | Frozen ORD, frozen RCF, frozen RER |
| rr-prompt.md | rr-spec.md | Frozen RP, post-release evidence |

**Pass criteria:** All prompts reference the correct spec and list the correct required inputs.

---

### S-06: Playbook-Spec Consistency

**What:** The playbook's process steps are consistent with the artifact specs and hard gates.

**How:** For each step in `docs/playbook.md`, verify:
- The artifact type, spec file, template file, prompt file, and validator file referenced are consistent with `S-02: Four-File Completeness`
- The gate condition stated (e.g., "Validator PASS + human freeze") is consistent across all steps
- The upstream dependency chain (ORD → RER → RCF → RP → RR) is stated correctly

**Pass criteria:** No contradictions between playbook steps and artifact specs. All file references resolve.

---

### S-07: README and Index Completeness

**What:** README.md and docs/index.md cover all artifacts, prompts, and key files.

**How:**
- Verify README.md includes the artifact type table (RER, RCF, RP, RR) and entry gate
- Verify docs/index.md lists all files in docs/specs/, docs/artifacts/, docs/prompts/, docs/validators/, docs/principles/, docs/playbook.md, docs/how-to-adapt.md, docs/how-to-use-with-ai.md
- Verify examples/ and tests/ are referenced

**Pass criteria:** No governed artifact type is missing from README or index. No key file is unlisted.

---

### S-08: Cross-Kit Handoff Contract Integrity

**What:** The REK upstream boundary contract (receives ORD from EEK) and downstream boundary contract (delivers RR §7 to Layer 6) are correctly specified.

**How:**
- Verify `release-entry-spec.md` requires an ORD ID in Frozen status from EEK
- Verify `rp-spec.md` requires ORD reference and explicitly references ORD §8 for rollback procedure
- Verify `rr-spec.md` §7 Handoff to Layer 6 hard gate (`handoff_declaration`) is a gated section
- Verify `docs/playbook.md` Downstream Handoff section describes what Layer 6 receives from RR §7

**Pass criteria:** Both boundary contracts are specified in the specs and enforced by validators. Layer 6 input contract is unambiguous.

---

## Part 2: Checklist

| Check | Status | Notes |
|-------|--------|-------|
| S-01: File reference integrity | — | |
| S-02: Four-file completeness | — | |
| S-03: Hard gate alignment | — | |
| S-04: Template section alignment | — | |
| S-05: Prompt-spec reference consistency | — | |
| S-06: Playbook-spec consistency | — | |
| S-07: README and index completeness | — | |
| S-08: Cross-kit handoff contract integrity | — | |

---

## Part 3: Flow Scenarios

### F-00: Happy Path — Full Release Flow

**What:** Complete flow from Release Entry Gate through Release Record with no deviations.

**Preconditions:**
- A frozen ORD exists (use `examples/basic-release/` as reference or a real ORD)
- A frozen RCF exists (RCF-TASKFLOW-001 or equivalent)

**Sequence:**

1. **Complete Release Entry Record**
   - Fill in `release-entry-template.md` with: ORD ID (frozen), release scope (canary), named release owner with authorization level, traceable authorization reference
   - Run `release-entry-validator.md` against the completed RER
   - Expected: PASS on all 5 gates
   - Freeze the RER

2. **Reuse existing frozen RCF** (or generate new if none exists)
   - If generating: complete intake form, run `rcf-prompt.md`, validate with `rcf-validator.md`, freeze
   - If reusing: confirm RCF ID, applicability scope covers this release, status is Frozen

3. **Generate Release Plan**
   - Run `rp-prompt.md` with frozen ORD, frozen RCF, frozen RER
   - Review generated RP: confirm rollback conditions are observable signals with thresholds, metrics are named (not categories), baseline values are sourced from ORD
   - Run `rp-validator.md` against draft RP
   - Expected: PASS on all 8 gates
   - Freeze the RP (leave Pre-Release Authorization Checklist blank)

4. **Complete Pre-Release Authorization Checklist**
   - Release owner completes all 10 checklist items and signs off
   - Expected: all items checked, date/time recorded

5. **Execute release** (simulated for test purposes)
   - Record evidence for each deployment step
   - Record signals observed at each exposure stage
   - Record decisions with decision maker names

6. **Generate Release Record**
   - Run `rr-prompt.md` with frozen RP and collected evidence
   - Run `rr-validator.md` against draft RR
   - Expected: PASS on all 6 gates
   - Freeze the RR

**Pass criteria:**
- All 4 validators return PASS
- No blocking issues remain in any artifact at freeze time
- RR §7 Handoff to Layer 6 is complete with all required fields populated

---

### F-01: Rollback During Execution

**What:** Release encounters a rollback condition during the canary stage. Rollback is triggered, documented, and the RR reflects the rolled-back outcome.

**Preconditions:**
- Frozen RER and RCF exist
- Frozen RP exists with defined rollback conditions (at least 2 observable signals with thresholds)
- Simulated execution evidence: error rate exceeds the RP rollback threshold during Stage 1

**Sequence:**

1. Use a valid frozen RP (can be from F-00 or `examples/basic-release/02-rp.md`)

2. **Simulate rollback trigger:**
   - At Stage 1 (10% exposure), error rate spikes to 0.8% (above RP rollback threshold of 0.5% for 5 minutes)
   - Decision made within 15-minute window: rollback

3. **Generate Release Record documenting rollback:**
   - Evidence: error rate metric value and timestamp from monitoring system
   - Exposure log: Stage 1 entry with observed signal (0.8% error rate), decision = Rollback, decision maker named
   - Monitoring observations: alert fired, response taken, rollback initiated at [timestamp]
   - Release Disposition: `rolled-back`; rollback trigger identified (RP Condition 1: error rate >0.5% for 5 minutes); rollback outcome confirmed

4. **Validate the RR:**
   - Run `rr-validator.md` against the rollback RR
   - Expected: PASS on all 6 gates
   - `release_summary` gate: outcome = rolled-back; explanation present
   - `exposure_log` gate: Stage 1 logged with metric values and named decision maker
   - `release_disposition` gate: rollback trigger identified against RP condition; rollback outcome documented
   - `handoff_declaration` gate: Layer 6 handoff reflects rolled-back state (exposure 0%, flags at pre-release values)

**Pass criteria:**
- RR validator returns PASS with outcome = rolled-back
- All rollback fields populated (trigger, timestamp, outcome)
- RR §7 Handoff to Layer 6 reflects the rolled-back state, not the planned full-exposure state

---

### F-02: Reuse Existing RCF

**What:** A team with an existing frozen RCF begins a new release engagement. The RCF generation step is skipped entirely.

**Preconditions:**
- A frozen RCF exists and is scoped to the system being released
- A new ORD exists for a different feature release (new RER needed)

**Sequence:**

1. **Complete a new Release Entry Record** for the new release
   - Reference the existing RCF ID in the RER: "RCF-TASKFLOW-001 applies — confirmed in Frozen status"
   - Named release owner must still be stated for this specific release
   - Run `release-entry-validator.md` — expected PASS

2. **Skip RCF generation** — confirm the existing RCF covers this release
   - Verify: RCF applicability scope includes the system being released
   - Verify: RCF is in Frozen status
   - No prompt run; no validation of the RCF (it was already validated when frozen)

3. **Generate Release Plan referencing the existing RCF**
   - Run `rp-prompt.md` with new ORD + existing RCF (RCF-TASKFLOW-001) + new RER
   - Validate with `rp-validator.md`
   - Expected: `upstream_references` gate PASS — RCF-TASKFLOW-001 referenced with Frozen status confirmed

4. **Verify scope consistency**
   - `scope_bounded` gate in RP validator: RP scope matches the new RER scope declaration
   - The existing RCF scope covers the new system

**Pass criteria:**
- RER PASS with existing RCF referenced
- RP PASS with `upstream_references` gate confirming both ORD and RCF are Frozen
- No requirement to regenerate or re-validate the existing RCF
- RP validator `scope_bounded` gate passes — no scope expansion from RER
