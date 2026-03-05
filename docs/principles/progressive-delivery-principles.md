# Progressive Delivery Principles

## Scope

These principles govern feature flag management and progressive rollout strategies for user-facing changes. They apply to any change that is exposed to users through a feature flag or percentage-based rollout. They do not govern backend changes with no user-facing exposure surface (those are governed solely by Release Safety Principles).

Kit artifacts that reference this file: Release Context File (§3 Exposure Standards), Release Plan (§3 Exposure Specification).

---

## Rules

### Feature Flag Lifecycle

- Every feature flag must have a defined lifecycle: `inactive` → `percentage rollout` → `segment targeting` → `full exposure` → `removed`. Flags that skip stages must document the justification in the Release Plan.
- Feature flags are temporary exposure control mechanisms. They are not permanent architecture. A flag that remains in the codebase after reaching full exposure is technical debt.
- Every flag must have a cleanup date assigned at creation. The cleanup date is the date by which the flag must be removed from the codebase after reaching full exposure. The cleanup date may be extended, but only with explicit documentation of the reason.
- Flag names must be descriptive and scoped to the feature or behavior they control (e.g., `NOTIF_BATCH_ENABLED`, not `FLAG_123`). Generic names are not permitted.
- The number of simultaneous active flags must be managed. No more than [defined in RCF] active percentage-rollout flags may exist in a single system at the same time to prevent interaction effects.

### Rollout Stages

- Percentage rollout increments must follow the schedule defined in the Release Plan. Increments may not be accelerated without documented signals confirming the previous stage is stable.
- Signals for rollout acceleration (proceed to next stage) and rollout pause (hold at current stage) must be defined in the Release Plan before exposure begins. These signals must be observable metrics, not subjective assessments.
- The minimum observation period between percentage increments is defined in the Release Context File. This is a floor, not a target — teams may observe longer.
- Rollout percentages are defined as percentages of the total user base unless the Release Plan specifies a subset (e.g., "25% of paid users"). Scope must be explicit.
- User targeting rules (segment targeting) must be defined as explicit, evaluable criteria. "Internal users first" is acceptable if "internal users" is a defined segment in the targeting system. Vague audience definitions are not acceptable.

### Monitoring During Rollout

- Baseline metric values must be established before exposure begins. Rollout signals (acceleration triggers, pause triggers, rollback triggers) must be defined as deviations from the baseline, not as absolute thresholds, unless the absolute threshold is well-established.
- The watch period for each rollout stage starts when the stage begins (not when the decision to proceed to that stage was made).
- Automated alerting for rollout signals must be confirmed active before each exposure stage begins.

### Flag Removal

- After a flag reaches full exposure and the observation period defined in the Release Plan has elapsed without issues, flag removal is required.
- Flag removal is a code change subject to the same review process as any other code change. It is not optional post-release housekeeping.
- A flag that cannot be removed without significant refactoring indicates the flag was used as architectural scaffolding, not exposure control. Document this as a technical debt item.

---

## Enforcement Mapping

| Rule Category | Enforced By |
|---------------|-------------|
| Flag lifecycle and cleanup date | `rcf-spec.md` §exposure_standards hard gate |
| Rollout stage definition with signals | `rp-spec.md` §exposure_specification hard gate |
| Minimum observation period | `rp-spec.md` §monitoring_plan hard gate (references RCF minimum) |
| Baseline metrics established before exposure | `rp-spec.md` §monitoring_plan hard gate |
| Exposure log per stage | `rr-spec.md` §exposure_log hard gate |
| Monitoring observations during rollout | `rr-spec.md` §monitoring_observations hard gate |
| Flag state documented in final disposition | `rr-spec.md` §release_disposition hard gate |
