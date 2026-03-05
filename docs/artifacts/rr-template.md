# Release Record

Post-release evidence artifact documenting what happened during the release. Produced after release execution is complete (or abandoned). Every entry traces to the Release Plan and documents what was observed, decided, or deviated.

The Release Record is an evidence artifact — it does not revise the Release Plan. Deviations from the plan are documented here; the RP remains frozen.

---

## Evidence Standards

Every evidence entry in this document must be:

- **Concrete** — An artifact (log excerpt, metric value, screenshot reference, alert record), not an assertion
- **Timestamped** — Date and time of the event or when evidence was collected
- **Traceable** — References the specific RP step or condition it documents
- **Retrievable** — Includes a location where the evidence can be accessed

---

## 1. Document Control

| Field | Value |
|-------|-------|
| RR ID | RR-{PROJECT}-{NNN} |
| Date | {date} |
| Author | {author} |
| Status | Draft / Validated / Frozen |
| Upstream RP | {RP ID} — Status: Frozen |
| Release Start | {date and time} |
| Release End | {date and time — when the release was declared complete or abandoned} |
| Release Owner | {named individual} |

---

## 2. Release Summary

- **What was released:** {system name, service, feature — consistent with RP}
- **Strategy used:** {canary / blue-green / rolling / direct-full}
- **Outcome:** {successful-full-exposure / successful-partial-exposure / rolled-back / abandoned}
- **Final exposure percentage:** {percentage at time of record creation}
- **Outcome explanation:** _(required if outcome is anything other than successful-full-exposure)_

---

## 3. Deployment Evidence

For each deployment step in RP §2, document what happened:

| RP Step | Action | Executed By | Outcome | Evidence | Timestamp | Deviation? |
|---------|--------|------------|---------|----------|-----------|------------|
| 1 | {RP step 1 description} | {person/system} | {Success / Failed / Partial} | {concrete evidence or reference} | {timestamp} | {Yes/No} |
| 2 | | | | | | |
| ... | | | | | | |

**Deviations from RP Deployment Specification:**

{For each row where Deviation = Yes, document: what was planned, what was done instead, and rationale}

| RP Step | Planned Action | Actual Action | Rationale |
|---------|---------------|--------------|-----------|
| | | | |

_If no deviations: None_

---

## 4. Exposure Log

For each exposure stage in RP §3, document what happened:

| Stage | Target | Actual Start | Signals Observed | Decision | Decision Maker | Rationale |
|-------|--------|-------------|-----------------|----------|---------------|-----------|
| Stage 1 | {RP target, e.g., 5%} | {date/time} | {metric values observed, e.g., "error rate: 0.01%, p99: 185ms"} | {Proceed / Pause / Investigate / Rollback} | {named person} | {why this decision was made} |
| Stage 2 | | | | | | |
| Stage 3 | | | | | | |

**Pauses and Investigations:**

{For any stage where decision was Pause or Investigate: document what was investigated, what was found, and how the situation was resolved before proceeding}

| Stage | Pause Start | Issue Investigated | Finding | Resolution | Resume Time |
|-------|-------------|-------------------|---------|-----------|------------|
| | | | | | |

_If no pauses or investigations: None_

**Skipped Stages (deviations from RP):**

{If any RP exposure stage was skipped: document which stage, why it was skipped, and who authorized the skip}

_If no skipped stages: None_

---

## 5. Monitoring Observations

**Metrics During Watch Period:**

For each metric in RP §6, report observed values across the watch period:

| Metric | Baseline | Stage 1 Observed | Stage 2 Observed | Stage 3 Observed | Final Value | Status |
|--------|----------|-----------------|-----------------|-----------------|-------------|--------|
| {metric name} | {baseline} | {value @ timestamp} | {value @ timestamp} | {value @ timestamp} | {final} | {Within threshold / Alert triggered} |

**Alerts Fired:**

| Alert | Fired At | Metric Value at Fire | Response Taken | Resolved At | Outcome |
|-------|----------|---------------------|---------------|------------|---------|
| {alert name} | {timestamp} | {value} | {what was done} | {timestamp} | {resolved / ongoing} |

_If no alerts fired: None_

**Watch Period Summary:**

- Total watch period duration: {actual duration}
- Watch period as planned: {RP-specified duration} — {met / shorter than planned: reason}
- Final assessment: {All metrics within thresholds / Degradation observed but within acceptable bounds / Issues identified — see disposition}

---

## 6. Release Disposition

**Final State:** {successful-full-exposure / successful-partial-exposure / rolled-back / abandoned}

**Current Exposure State:**

| System / Service | Current Exposure | Feature Flags Active | Flag Values |
|-----------------|-----------------|---------------------|------------|
| {system} | {percentage or "full"} | {flag names} | {current values} |

**For rolled-back:**
- Rollback trigger that fired: {which condition from RP §4 was met}
- Rollback trigger value: {observed metric value vs. threshold}
- Rollback initiated at: {timestamp}
- Rollback completed at: {timestamp}
- Post-rollback verification: {what was confirmed after rollback}

**For abandoned:**
- Trigger for abandonment: {what caused the decision to abandon}
- State at abandonment: {what was deployed, what exposure was reached}
- Cleanup performed: {what was reversed or removed}

**For partial:**
- Reason full exposure not reached: {explanation}
- Intended action going forward: {e.g., "schedule resume after fix", "abandon feature"}

---

## 7. Handoff to Layer 6

**What is in production:**

| System / Service | Version / Commit | Deployed Configuration | Notes |
|-----------------|-----------------|----------------------|-------|
| {system} | {version or commit hash} | {key configuration values} | |

**Current exposure state:** {consistent with §6 — percentage, flags, states}

**Monitoring active:**

| Metric | Dashboard / System | Alert Configured? | Alert Threshold |
|--------|-------------------|------------------|----------------|
| {metric name} | {where to view} | {Yes / No} | {threshold} |

**SLO Baseline:**

| SLO | Baseline Value | Source | Reference |
|-----|---------------|--------|-----------|
| {SLO name, e.g., error rate} | {baseline value} | {ORD §4 / first-release measurement} | {where stored} |

**Open Incidents at Handoff:**

{List any incidents open at the time of handoff, or "None"}

**Recommended Layer 6 Watch Items:**

These aspects of the system's production behavior warrant elevated attention during the initial operating period:

| Watch Item | Why | Recommended Duration |
|------------|-----|---------------------|
| {what to watch} | {why it deserves attention} | {how long} |

_If no elevated watch items: None identified_

---

## Freeze Declaration

| Field | Value |
|-------|-------|
| Frozen | Yes / No |
| Freeze Date | {date} |
| Approved By | {approver} |
