# Release Record — Specification

Version: v1.0

The Release Record (RR) is the terminal artifact of the Release & Exposure Kit. It documents what actually happened during a release: the deployment evidence, exposure decisions made at each stage, monitoring observations during the watch period, and the final state of the system. It declares the release disposition and provides the handoff package to the Reliability & Resilience Kit (Layer 6).

The RR is an **evidence artifact** — it does not define new requirements or revise the plan. Every entry traces to the Release Plan and documents what was observed, decided, or deviated.

---

## What This Artifact Is Not

- **Not a postmortem or incident analysis.** The RR is a structured evidence record of what actually happened during the release. Analysis of systemic causes, lessons learned, and reliability implications belong downstream in the Layer 6 RHR or ODK PMR.
- **Not a revised Release Plan.** Deviations from the RP are documented in the RR; the RP remains frozen. The RR does not retroactively edit or replace the RP.
- **Not an assertion document.** Every entry in the RR must be backed by concrete evidence (logs, metric values, decision records) — not summary statements or inferences.

---

## Purpose

The Release Record serves three roles:

1. **Release audit trail** — Captures every significant decision and observation during the release for traceability and accountability
2. **Disposition declaration** — Formally declares the release outcome (successful / partial / rolled back / abandoned) so downstream consumers know the system state
3. **Layer 6 handoff** — Documents what is now in production, what monitoring is active, and the current SLO baseline so the Reliability & Resilience Kit can begin oversight

---

## Upstream Dependencies

- Frozen Release Plan (RP) — the plan against which execution is documented
- Actual release execution evidence (logs, metric screenshots, alert records, decision records)

---

## Required Sections

1. Document Control
2. Release Summary
3. Deployment Evidence
4. Exposure Log
5. Monitoring Observations
6. Release Disposition
7. Handoff to Layer 6

---

## Evidence Standards

Every evidence entry in this document must meet these properties:

- **Concrete** — An artifact (log excerpt, metric value, screenshot reference, alert record), not an assertion ("deployment succeeded")
- **Timestamped** — When the evidence was collected or when the event occurred
- **Traceable** — References the specific RP step or condition it documents
- **Retrievable** — Includes a location where the evidence can be accessed (URL, path, system reference, or archived record)

---

## Content Rules

### Document Control
**Rules**
- RR ID must be present (format: RR-{PROJECT}-{NNN})
- RP ID must be referenced (the frozen plan this record documents)
- Start time and end time of the release must be present
- Release owner must be named (the person who held accountability throughout)
- Status must be one of: Draft | Validated | Frozen

**Failure Examples**
- RP ID absent
- Start or end time absent
- Release owner named as a team rather than a person

### Release Summary
**Rules**
- What was released must be stated (system, service, feature name — consistent with RP)
- The deployment strategy used must be stated (canary / blue-green / rolling / direct-full)
- The outcome must be declared: successful-full-exposure | successful-partial-exposure | rolled-back | abandoned
- For outcomes other than successful-full-exposure, a one-sentence explanation of the outcome is required
- Final exposure percentage at time of record creation must be stated

**Failure Examples**
- Outcome absent or ambiguous ("mostly worked")
- Outcome is "successful" but final exposure percentage is not 100%
- Strategy used differs from RP with no explanation

### Deployment Evidence
**Rules**
- Each deployment step listed in the RP must have a corresponding evidence entry
- Evidence must be concrete (not assertions): log excerpts, deployment system output, health check results
- Deviations from the RP deployment specification must be documented with: what was planned, what was done instead, and the rationale
- If a step produced no evidence (e.g., infrastructure-as-code change with no output), that must be noted explicitly rather than left blank

**Failure Examples**
- Steps with assertion-only entries ("deployment completed successfully")
- RP steps that have no corresponding evidence entry
- Deviations from RP undocumented
- Section contains a summary instead of per-step evidence

### Exposure Log
**Rules**
- Each exposure stage defined in the RP must have an entry in the log
- Each entry must include: stage name/target, start time, signals observed at that stage, decision made (proceed / pause / investigate / rollback), who made the decision, and the rationale
- If a stage was skipped (deviation from RP), this must be documented with justification
- If a stage triggered a pause or investigation, the resolution and resumption must be documented
- Signal values at each stage must be stated as observed metric values, not as qualitative assessments

**Failure Examples**
- Stages documented without observed signal values
- Decision maker absent ("the team decided")
- Pauses or investigations with no documented resolution
- Stage skipped without documentation
- "All metrics looked good" — not evidence

### Monitoring Observations
**Rules**
- Metric values must be reported for the full watch period at each exposure stage
- Values must be stated as specific numbers (not ranges unless the monitoring system only provides ranges) with timestamps
- Any alert that fired during the watch period must be documented: which alert, when, what response was taken, and outcome
- The final metric values at close of watch period must be documented
- If the watch period ended before the RP-specified duration (release abandoned or rolled back), the actual duration must be stated
- If the watch period was shortened for reasons other than rollback or abandonment (e.g., mock provider verification, test environment constraints), the deviation must document: (1) what was shortened and by how much, (2) what alternative evidence substitutes for the shortened observation period, and (3) when and under what conditions the full-duration watch period will be executed

**Failure Examples**
- "Metrics were within acceptable range" — not evidence
- Alert fired but not documented
- Watch period stated as completed when release was rolled back early
- Final metric values absent
- Watch period shortened without documenting the deviation rationale and compensating evidence

### Release Disposition
**Rules**
- Final state must be declared explicitly: one of successful-full-exposure | successful-partial-exposure | rolled-back | abandoned
- Current exposure state must be documented: percentage of users currently exposed, which feature flags are active and at what values
- For rolled-back: the rollback trigger that fired (matching the RP rollback conditions) and the rollback outcome must be stated
- For abandoned: what triggered abandonment and what cleanup was performed must be stated
- For partial: the reason full exposure was not reached and the intended action going forward must be stated

**Failure Examples**
- Final state declared as "successful" while flags are still at partial rollout
- Rollback trigger not identified against RP conditions
- Active flag states absent from final disposition
- "Exposure TBD" — not a disposition

### Handoff to Layer 6
**Rules**
- What is now in production must be stated (system name, version or commit, deployed configuration)
- Current exposure state must be stated (consistent with §6 Release Disposition)
- What monitoring is currently active must be listed (metric names, dashboards, alert configurations)
- The pre-release SLO baseline must be referenced or stated (sourced from ORD or first-release documentation)
- Any open incidents at handoff time must be listed (or "None")
- Recommended Layer 6 watch items must be listed — what aspects of the system's behavior in production should receive elevated attention during the first period of operation
- If any SLO baselines are deferred (not yet established at release time), each deferred baseline must include: (1) a concrete capture trigger describing when the baseline will be collected (e.g., "on first successful generation request with a real LLM provider"), (2) the metric name(s) to be baselined, and (3) where the captured baseline will be recorded (e.g., "SRER intake form §SLO Baseline" or a specific monitoring system). Deferred baselines without a capture trigger and target location are not acceptable — they rely on operator memory and are likely to be forgotten.

**Failure Examples**
- Handoff section absent
- Monitoring list empty or generic ("standard monitoring")
- SLO baseline absent
- Open incidents not assessed (section blank rather than "None")
- No recommended watch items (even "None identified" is acceptable if documented)
- Deferred baseline listed as "to be determined later" without a capture trigger or target location

---

## Format Requirements

- All timestamps must include date and time (not date only)
- Metric values must include units (e.g., "error rate: 0.02%", "p99 latency: 210ms")
- Evidence locations must be specific (not "check the dashboard")
- Decision records must name the individual who made each significant decision

---

## Completeness Rules

- All seven sections must be present and non-empty
- All RP deployment steps documented
- All RP exposure stages documented (or deviations explained)
- Final exposure state consistent between §6 and §7
- Handoff section covers all required fields

---

## Relationship Rules

- The RR documents the RP — it does not revise or replace it; the RP remains frozen
- Deviations from the RP are documented in the RR; the RP is not retroactively edited
- The RR is the input to Layer 6 (Reliability & Resilience) — the Layer 6 kit's intake uses the RR §7 Handoff section as its upstream boundary contract
- A frozen RR is immutable — post-release observations belong in Layer 6 records, not in amendments to the RR

---

## Hard Gates

1. **release_summary** — Outcome declared from enumerated list; strategy stated; final exposure percentage documented; deviation from RP strategy explained if different
2. **deployment_evidence** — Each RP deployment step has a corresponding evidence entry; evidence is concrete (not assertions); deviations from RP documented with rationale
3. **exposure_log** — Each RP exposure stage has an entry; signals observed stated as metric values; decision + who made it documented for each stage; pauses and resolutions documented
4. **monitoring_observations** — Metrics reported with specific values and timestamps for full watch period; alerts documented with responses; final values present
5. **release_disposition** — Final state declared from enumerated list; current exposure state documented; active flag states listed; rollback trigger or abandonment reason documented if applicable
6. **handoff_declaration** — What is in production stated; monitoring active listed; SLO baseline referenced; open incidents assessed; Layer 6 recommended watch items listed
