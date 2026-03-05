# Release Plan

Per-release artifact defining the deployment strategy, exposure stages, rollback conditions, communication plan, and monitoring protocol for this specific release.

Generated from a frozen ORD and a frozen RCF. Must be validated and frozen before the Pre-Release Authorization Checklist is completed and execution begins.

---

## 1. Document Control

| Field | Value |
|-------|-------|
| RP ID | RP-{PROJECT}-{NNN} |
| Date | {date} |
| Author | {author} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Upstream ORD | {ORD ID} — Status: Frozen |
| Upstream RCF | {RCF ID} — Status: Frozen |
| Release Summary | {1-2 sentences describing what is being released} |

---

## 2. Deployment Specification

**Strategy:** {canary / blue-green / rolling / direct-full} — selected per RCF §2 for this system's risk tier ({Tier N})

**Deployment Steps:**

| Step | Action | Performed By | Verification | Evidence Captured |
|------|--------|-------------|-------------|------------------|
| 1 | {action — reference ORD §8 step N} | {person / system} | {how success is confirmed} | {what evidence is generated} |
| 2 | | | | |
| ... | | | | |

**Configuration Changes:**

| Item | Type | Current Value | New Value | Applied By |
|------|------|--------------|-----------|-----------|
| {config/env var/flag} | {env var / feature flag / infrastructure} | {current} | {new} | {who/how} |

**Expected Timeline:**

- Deployment window: {start time} to {latest end time}
- Estimated time per phase: {breakdown}

---

## 3. Exposure Specification

**Exposure strategy:** {percentage rollout / segment targeting / immediate full / mixed}

| Stage | Target | Trigger to Proceed | Hold Action if Trigger Not Met |
|-------|--------|-------------------|-------------------------------|
| Stage 1 | {e.g., canary: 5% of traffic} | {observable signal + threshold, e.g., "error rate <0.1% sustained for 2h"} | {e.g., "investigate with SRE before proceeding"} |
| Stage 2 | {e.g., 25%} | {observable signal + threshold} | |
| Stage 3 | {e.g., 100% / full exposure} | {e.g., "24h watch period complete, all success criteria met"} | |

**Feature Flag Configuration:**

| Flag Name | Stage 1 Value | Stage 2 Value | Stage 3 Value | Set By |
|-----------|--------------|--------------|--------------|--------|
| {FLAG_NAME} | {value} | {value} | {value} | {person / deployment system} |

**Segment Targeting (if applicable):**

Stage {N} targets: {evaluable criteria, e.g., "users with account_type = 'internal' in user service"}

**RCF Compliance:**
- Maximum increment: {increment per stage} ≤ RCF maximum of {RCF value} ✓
- Observation period: {period per stage} ≥ RCF minimum of {RCF value} ✓

---

## 4. Rollback Specification

**Rollback Conditions:**

Rollback must be triggered when any of the following conditions is observed:

| Condition | Signal | Threshold | Sustained Duration |
|-----------|--------|-----------|-------------------|
| 1 | {observable metric, e.g., application error rate} | {threshold, e.g., >0.5%} | {e.g., 5 minutes sustained} |
| 2 | {observable metric, e.g., p99 latency} | {threshold, e.g., >500ms} | {e.g., any single measurement} |
| 3 | {additional condition} | | |

**Rollback Procedure:**

Reference: ORD §8 Rollback procedure, steps {N–N}

Summary:
1. {step 1}
2. {step 2}
3. {step 3 — verification after rollback}

**Responsible Party:** {named individual} — backup: {named individual or role}

**Decision Authority:** {who authorizes the rollback decision} — escalation if unavailable: {escalation path}

**Decision Window:** Rollback decision must be made within {duration} of a condition being triggered. If no decision is made within this window, rollback is initiated automatically.

---

## 5. Communication Plan

**Pre-Release Notifications (sent before execution begins):**

| Audience | Content | Channel | Timing | Owner |
|----------|---------|---------|--------|-------|
| {audience per RCF §5} | {what is communicated} | {channel} | {N hours before release} | {person} |

**During-Release Notifications:**

| Audience | Content | Trigger Event | Channel | Owner |
|----------|---------|--------------|---------|-------|
| | | | | |

**Post-Release Notifications:**

| Audience | Content | Trigger Event | Channel | Owner |
|----------|---------|--------------|---------|-------|
| | | | | |

**Customer-Facing Communication:**

{If this release meets an RCF customer-notification trigger: describe communication content, channel, and timing.}
{If no RCF trigger is met: state which triggers were checked and why none apply.}

---

## 6. Post-Release Monitoring Plan

**Metrics to Watch:**

| Metric Name | Baseline Value (from ORD) | Alert Threshold | Monitoring System |
|-------------|--------------------------|-----------------|------------------|
| {exact metric name} | {baseline value} | {threshold — absolute or % deviation} | {system/dashboard} |
| | | | |

**Watch Period:** {duration per exposure stage} — meets RCF minimum of {RCF value}

**Check-In Schedule:** {how often the release owner or designated monitor reviews metrics during watch period}

**Success Criteria (proceed to next stage):**

All of the following must be true for the full observation period before proceeding to the next stage:
- {criterion 1, e.g., "error rate <0.1%"}
- {criterion 2, e.g., "p99 latency <300ms"}
- {criterion 3, e.g., "no customer-impacting alerts fired"}

---

## 7. Open Issues

Before freezing this plan, list any unresolved items. No items marked "Blocks Release?" may remain open at freeze time.

| Issue | Owner | Resolution Deadline | Blocks Release? |
|-------|-------|--------------------|-----------------|
| | | | Yes / No |

_If no open issues: None_

---

## 8. Pre-Release Authorization Checklist

_Completed by the release owner immediately before execution begins — not at plan freeze time._

- [ ] ORD status re-confirmed as Frozen (re-verified today)
- [ ] RP validated (PASS result on file) and frozen
- [ ] Feature flags configured per §3 for initial stage
- [ ] Rollback procedure reviewed and confirmed operational (per ORD §8)
- [ ] Monitoring dashboards active; baseline values visible in monitoring system
- [ ] Automated alerts configured per §6; test-fire confirmed
- [ ] Pre-release internal communications sent per §5
- [ ] Release window confirmed with stakeholders
- [ ] Rollback responsible party ({named person}) confirmed available
- [ ] Release owner authorizes execution to begin

**Release Owner Sign-Off:**
- Name:
- Date and Time:
- Notes: _(optional — any conditions or constraints on execution)_

---

## Freeze Declaration

| Field | Value |
|-------|-------|
| Frozen | Yes / No |
| Freeze Date | {date} |
| Approved By | {approver} |
