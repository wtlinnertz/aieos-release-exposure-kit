# Release Context File — Specification

The Release Context File (RCF) is an organizational policy artifact that defines the standards, constraints, and requirements that govern all releases within its stated scope. It translates organizational release policy into enforceable guardrails that constrain Release Plans.

The RCF is **organizational-level and reusable** — it is authored once per organizational scope and reused across multiple releases without modification, until the organization's release policy changes.

---

## What This Artifact Is Not

- **Not a Release Plan.** The RCF defines organizational policy reusable across all releases. A Release Plan (RP) translates the RCF's standards into the specific strategy for one release engagement.
- **Not a deployment runbook.** The RCF defines standards (what is required); deployment procedures are in the ORD (how to execute it).
- **Not a project-level document.** The RCF is organizational scope — it applies to all releases within its stated scope, not to one initiative. Project-specific release decisions belong in the RP.

---

## Purpose

The RCF serves two roles:

1. **Release guardrails** — Defines the deployment strategies the organization permits, the exposure standards it requires, the authorization model it enforces, and the monitoring standards it mandates
2. **Standards contract** — Provides a stable reference that Release Plans must comply with, so individual release decisions don't re-litigate organizational policy

---

## Upstream Dependencies

- Organization's release policy (human input via `release-context-intake-template.md`)
- `docs/principles/release-safety-principles.md`
- `docs/principles/progressive-delivery-principles.md`

---

## Required Sections

1. Document Control
2. Deployment Standards
3. Exposure Standards
4. Release Authorization Model
5. Communication Requirements
6. Monitoring Requirements
7. Scope and Exceptions

---

## Content Rules

### Document Control
**Rules**
- RCF ID must be present (format: RCF-{ORG}-{NNN} or RCF-{SCOPE}-{NNN})
- Owner (team or role, not individual) must be present
- Version must be present
- Status must be one of: Draft | Validated | Frozen
- Applicability scope must state which systems, teams, or products this RCF governs

**Failure Examples**
- Missing RCF ID
- Applicability scope is "all systems" without further definition — too vague to be enforceable
- Owner is a person's name (should be a team or role to survive personnel changes)

### Deployment Standards
**Rules**
- Permitted deployment strategies must be enumerated (canary / blue-green / rolling / direct-full)
- For each permitted strategy, the required verification steps must be defined (what must be confirmed after deployment before considering it successful)
- System risk tiers must be defined (at minimum: high-risk and standard-risk) with criteria for each tier
- For each risk tier, the permitted deployment strategies must be specified (e.g., "Tier 1 systems: canary or blue-green only; Tier 2 systems: any permitted strategy")
- If any strategy is not permitted for any tier, that restriction must be stated explicitly

**Failure Examples**
- "Use canary when appropriate" — not a standard, no criteria
- Risk tiers mentioned but not defined with criteria
- Permitted strategies listed without verification requirements

### Exposure Standards
**Rules**
- Progressive delivery requirements must state when progressive exposure is mandatory (minimum: by risk tier)
- Maximum exposure increment per stage must be defined (e.g., "no single increment may exceed 25 percentage points")
- Minimum observation period between exposure stages must be defined (a clock duration, not a subjective description like "sufficient time")
- Feature flag governance rules must be defined: flag lifecycle stages, cleanup date requirement, maximum simultaneous active flags per system
- The conditions under which direct-full exposure (skipping progressive rollout) is permitted must be stated explicitly; if it is never permitted, state that

**Failure Examples**
- Maximum exposure increment absent
- Minimum observation period stated as "enough time to detect issues" rather than a duration
- Feature flag cleanup date requirement absent
- "Progressive delivery encouraged" — not a standard

### Release Authorization Model
**Rules**
- Authorization levels must be defined with specific exposure thresholds (e.g., "engineer: up to 10%; senior engineer: up to 50%; engineering manager: up to 100%")
- Escalation triggers must be defined: what conditions require escalating to a higher authorization level
- Emergency release procedure must be defined: what steps are required for a release that bypasses normal planning (emergency does not mean authorization-free)
- The procedure for releases that cross organizational boundaries (affecting multiple teams or systems) must be stated

**Failure Examples**
- "Use judgment" — not an authorization model
- Authorization levels defined without thresholds
- Emergency procedure absent
- Escalation triggers absent

### Communication Requirements
**Rules**
- Internal stakeholder notification requirements must be defined: who is notified, at what stage of the release (e.g., at release start, at each exposure increment, at completion), and in what form
- Customer-facing communication triggers must be defined: what kinds of changes require customer notification (e.g., behavior changes, performance impacts, breaking API changes)
- Partner or API consumer notification requirements must be defined (or "not applicable" with justification)
- The timing of each communication relative to release events must be specified (before, at start, at each stage, at completion)

**Failure Examples**
- "Notify the team" — not a requirement; who, when, how?
- Customer notification triggers absent
- "Communication as needed" — not a standard

### Monitoring Requirements
**Rules**
- Minimum watch period after each exposure stage must be defined as a clock duration
- The metric categories that must be monitored must be specified (at minimum: error rate, latency, and one business metric)
- Alert threshold policy must be defined: whether thresholds must be relative to baseline, absolute, or either — and what the default policy is when no baseline exists
- SLO baseline documentation requirement must be defined: when and how the pre-release SLO baseline must be captured and stored

**Failure Examples**
- Minimum watch period defined as "until we're confident" rather than a duration
- Required metric categories absent
- SLO baseline documentation requirement absent
- "Monitor key metrics" — not a standard

### Scope and Exceptions
**Rules**
- The scope section must state which systems, teams, or products this RCF governs
- It must state what is explicitly excluded from this RCF's scope
- Documented exceptions to any standard must be listed here (not in the standards sections themselves) with justification and expiry condition

**Failure Examples**
- Scope restates "all systems" without defining what that means
- Exceptions embedded in standards sections rather than in this section

---

## Format Requirements

- Risk tiers must be enumerated and named consistently throughout the document
- Authorization thresholds must be stated as percentages or counts — not as "low, medium, high"
- Durations must be stated as clock time (hours, days) — not as "short" or "adequate"
- All standards must use "must" for required items and "may" for optional items — not "should" or "ideally"

---

## Completeness Rules

- All seven sections must be present and non-empty
- At minimum two risk tiers must be defined
- Authorization model must include at least two levels
- Metric categories must include at least three named categories
- No standard may be left as aspirational prose — each must be specific enough to evaluate compliance

---

## Relationship Rules

- The RCF is an upstream input to the Release Plan — all RP standards must comply with the RCF
- The RCF does not replace the principles files — principles define the "why," the RCF translates principles into org-specific rules
- When the RCF changes, all Release Plans generated from it must be reviewed for continued compliance; those already frozen and executed do not require re-validation

---

## Hard Gates

1. **document_control** — RCF ID, owner (team or role), version, status, and applicability scope present
2. **deployment_standards** — Permitted strategies enumerated; verification requirements per strategy; risk tiers defined with criteria; permitted strategies per tier specified
3. **exposure_standards** — Progressive delivery requirements by risk tier; maximum increment defined; minimum observation period defined as a duration; flag governance rules (lifecycle, cleanup, simultaneous limit)
4. **authorization_model** — Authorization levels with specific thresholds; escalation triggers; emergency procedure; cross-boundary procedure
5. **communication_requirements** — Internal notification (who, when, form); customer-facing triggers; partner/API requirements stated
6. **monitoring_requirements** — Minimum watch period as duration; required metric categories (at least 3); alert threshold policy; SLO baseline documentation requirement
7. **standard_enforceability** — All standards are specific enough to evaluate compliance; no aspirational language ("should," "ideally," "as appropriate")
