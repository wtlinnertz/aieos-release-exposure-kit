# Release Safety Principles

## Scope

These principles govern all production deployments managed by this kit. They apply to any change that modifies the behavior of a production system, regardless of size or perceived risk. They do not cover internal or non-production deployments.

Kit artifacts that reference this file: Release Context File (§2 Deployment Standards, §4 Release Authorization Model), Release Plan (§3 Rollback Specification).

---

## Rules

### Deployment Safety

- All production deployments must have a documented rollback procedure before execution begins. A deployment without a tested rollback procedure may not begin.
- Rollback procedures must be exercised (tested in a pre-production environment) before the release window. "We can roll back" is not sufficient — the procedure must have been run at least once.
- Rollback conditions must be stated as concrete, observable signals with defined thresholds. Conditions phrased as "if something goes wrong" or "if we decide to" are not acceptable.
- Every deployment must produce observable output: logs, metrics, or traces must confirm what happened. A deployment with no observable evidence of outcome has not been verified.
- No production configuration change may be applied without a record of who applied it and when. Infrastructure-as-code changes follow the same release process as code changes.

### Release Authorization

- Release authorization is not implied by any upstream event — not by sprint completion, PR merge, ORD approval, or any automated gate. Authorization is an explicit human decision with a named decision-maker.
- A named release owner must be assigned before release planning begins. The release owner is accountable for the release from plan through final disposition.
- Authorization scope is specific: authorizing a canary deployment does not authorize full exposure. Each exposure stage requires explicit authorization unless the Release Plan specifies pre-authorization conditions that have been confirmed.
- Emergency releases (bypassing normal planning steps) must still have a named release owner and a documented rollback procedure. The emergency path does not remove accountability — it compresses the process.

### Exposure Safety

- Progressive exposure is required for any user-facing change classified as Tier 1 or Tier 2 risk in the Release Context File. Direct full-exposure deployment is permitted only for Tier 3 (lowest risk) changes.
- No exposure stage may be skipped. If a Release Plan defines canary → 25% → 100%, the 25% stage may not be bypassed even if the canary looks clean.
- The minimum observation period between exposure stages is defined in the Release Context File. This period may not be shortened without an explicit exception documented in the Release Plan.

### Incident and Abort

- If a rollback condition is triggered during a release, the release owner must make a documented go/no-go decision within the time window defined in the Release Plan. Silence is not a decision to continue.
- A release that is abandoned mid-execution must produce a Release Record documenting what state was reached, what triggered the abort, and what cleanup was performed.
- Partial exposures that are not rolled back to zero must document the current exposure state and handoff monitoring responsibility explicitly.

---

## Enforcement Mapping

| Rule Category | Enforced By |
|---------------|-------------|
| Rollback procedure requirement | `rp-spec.md` §rollback_specification hard gate |
| Rollback conditions as observable signals | `rp-spec.md` §rollback_specification hard gate |
| Named release owner | `release-entry-spec.md` §release_owner hard gate |
| Authorization is explicit | `release-entry-spec.md` §priority_confirmed hard gate |
| Observable deployment output | `rr-spec.md` §deployment_evidence hard gate |
| Exposure stages not skipped | `rp-spec.md` §exposure_specification hard gate |
| Observation period compliance | `rp-spec.md` §monitoring_plan hard gate (references RCF minimum) |
| Abandoned release documentation | `rr-spec.md` §release_disposition hard gate |
| Partial exposure handoff | `rr-spec.md` §handoff_declaration hard gate |
