# Release Context File

Organizational release policy that governs all releases within its stated scope. Defines deployment standards, exposure standards, authorization model, communication requirements, and monitoring requirements.

The RCF is reusable across multiple release engagements. It is not regenerated per release.

---

## 1. Document Control

| Field | Value |
|-------|-------|
| RCF ID | RCF-{ORG/SCOPE}-{NNN} |
| Owner | {team or role name — not an individual} |
| Version | {version} |
| Date | {date} |
| Status | Draft / Validated / Freeze Pending / Frozen |
| Applicability Scope | {which systems, teams, or products this RCF governs} |

---

## 2. Deployment Standards

### System Risk Tiers

| Tier | Name | Criteria |
|------|------|----------|
| 1 | {name} | {criteria for classifying a system as Tier 1} |
| 2 | {name} | {criteria for classifying a system as Tier 2} |
| 3 | {name} | {criteria for classifying a system as Tier 3} |

### Permitted Strategies by Tier

| Tier | Permitted Strategies | Notes |
|------|---------------------|-------|
| 1 | {strategy list} | {any restrictions or required conditions} |
| 2 | {strategy list} | |
| 3 | {strategy list} | |

### Verification Requirements

For each permitted strategy, the following must be confirmed before considering deployment successful:

**Canary deployment verification:**
{list of required verification steps}

**Blue-green deployment verification:**
{list of required verification steps}

**Rolling deployment verification:**
{list of required verification steps}

**Direct-full deployment verification:**
{list of required verification steps}

---

## 3. Exposure Standards

### Progressive Delivery Requirements

| Tier | Progressive Exposure Required? | Conditions |
|------|-------------------------------|-----------|
| 1 | {Yes / No} | {conditions under which exceptions apply, if any} |
| 2 | {Yes / No} | |
| 3 | {Yes / No} | |

### Rollout Constraints

- **Maximum exposure increment per stage:** {percentage points} — no single stage may increase exposure by more than this amount
- **Minimum observation period between stages:** {duration} — the minimum time that must elapse before proceeding to the next stage
- **Direct-full exposure permitted for Tier 1:** {Yes with conditions / No} — {conditions or "Not permitted"}

### Feature Flag Governance

- **Flag lifecycle stages:** inactive → percentage rollout → segment targeting → full exposure → removed
- **Cleanup date requirement:** Flags must be removed within {duration} of reaching full exposure
- **Maximum simultaneous active flags per system:** {number} — to prevent interaction effects
- **Flag naming convention:** {naming pattern, e.g., `{FEATURE}_{BEHAVIOR}` in all caps with underscores}

---

## 4. Release Authorization Model

### Authorization Levels

| Level | Role / Title | Authorized Up To | Escalation Required Above |
|-------|-------------|-----------------|--------------------------|
| 1 | {role} | {exposure %, or "Tier 3 only"} | {threshold} |
| 2 | {role} | {exposure %} | {threshold} |
| 3 | {role} | 100% / full release | — |

### Escalation Triggers

The following conditions require escalating to the next authorization level:
{list of specific escalation triggers, e.g., "any rollback event", "Tier 1 system exposure above 25%"}

### Emergency Release Procedure

For releases that cannot follow normal planning steps (e.g., critical security patch):

1. {step 1}
2. {step 2}
3. {step 3 — post-emergency documentation requirement}

_Emergency releases require a named release owner and a documented rollback procedure. Emergency authorization does not waive accountability._

### Cross-Boundary Releases

For releases that affect multiple teams or systems: {procedure for coordination and authorization}

---

## 5. Communication Requirements

### Internal Stakeholder Notifications

| Audience | Notification Stage | Content | Channel |
|----------|-------------------|---------|---------|
| {audience} | {before release / at start / at each stage / at completion} | {what is communicated} | {channel} |

### Customer-Facing Communication Triggers

Customer notification is required when the release involves any of the following:
{list of triggers, e.g., "behavior change visible to end users", "performance impact >X%", "API breaking change", "data migration"}

### Partner / API Consumer Notification

{requirements for notifying partners or API consumers, or "Not applicable: {justification}"}

---

## 6. Monitoring Requirements

### Watch Period

- **Minimum watch period per exposure stage:** {duration} — the minimum time the release team must actively monitor before closing a stage

### Required Metrics

The following metric categories must be monitored for every release (Release Plans must identify specific metrics within each category):

| Category | Description |
|----------|-------------|
| Error rate | {specification, e.g., "application error rate and 5xx rate"} |
| Latency | {specification, e.g., "p50, p95, p99 for all endpoints"} |
| {business metric} | {specification} |
| {additional category} | {specification} |

### Alert Threshold Policy

- **Default policy:** {relative to baseline / absolute / either}
- **When no baseline exists:** {default approach for first-time releases}
- **Threshold tightening for Tier 1:** {any additional requirements for high-risk systems}

### SLO Baseline Documentation

- **When to capture:** {before release begins / from ORD evidence / at first release}
- **How to store:** {location and format}
- **Retention:** {how long the baseline must be retained}

---

## 7. Scope and Exceptions

### Scope

This RCF governs: {explicit statement of what is covered}

Explicitly excluded: {what is not covered by this RCF}

### Documented Exceptions

| Exception | Affected Standard | Justification | Expiry Condition |
|-----------|-----------------|---------------|-----------------|
| {exception description} | {which section} | {why this exception exists} | {when this exception expires or is reviewed} |

---

## Freeze Declaration

| Field | Value |
|-------|-------|
| Frozen | Yes / No |
| Freeze Date | {date} |
| Approved By | {approver} |
| Notes | {optional: any conditions on use of this RCF} |
