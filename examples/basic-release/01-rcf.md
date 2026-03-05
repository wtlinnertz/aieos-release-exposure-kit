# Release Context File

Organizational release policy that governs all releases within its stated scope. Defines deployment standards, exposure standards, authorization model, communication requirements, and monitoring requirements.

The RCF is reusable across multiple release engagements. It is not regenerated per release.

---

## 1. Document Control

| Field | Value |
|-------|-------|
| RCF ID | RCF-TASKFLOW-001 |
| Owner | Platform Engineering team |
| Version | 1.0 |
| Date | 2024-02-01 |
| Status | Frozen |
| Applicability Scope | All production services in the TaskFlow SaaS platform — backend services, frontend applications, and data pipelines owned by the TaskFlow engineering organization |

---

## 2. Deployment Standards

### System Risk Tiers

| Tier | Name | Criteria |
|------|------|----------|
| 1 | Critical | Services that handle authentication, payments, user data storage, or serve as shared infrastructure (API gateway, database clusters). Failure directly impacts all users or results in data loss. |
| 2 | Standard | User-facing product features and supporting backend services. Degradation affects a subset of users or specific functionality but does not compromise data integrity or authentication. |
| 3 | Internal | Developer tooling, background batch jobs, internal dashboards, and services with no direct user exposure. Failure has no user-visible impact. |

### Permitted Strategies by Tier

| Tier | Permitted Strategies | Notes |
|------|---------------------|-------|
| 1 | canary, blue-green | Direct-full prohibited. Rolling permitted only for stateless services with no shared state dependencies. |
| 2 | canary, blue-green, rolling, direct-full | Direct-full permitted only for Tier 2 systems under the conditions in §3. |
| 3 | canary, blue-green, rolling, direct-full | No restrictions. |

### Verification Requirements

**Canary deployment verification:**
- Canary instance returns healthy health-check response at `/health`
- Canary instance appears in load balancer pool and is receiving traffic (confirmed via load balancer metrics)
- Application startup logs contain no ERROR-level entries
- Feature flag service is reachable and returning expected values for canary cohort

**Blue-green deployment verification:**
- Green environment passes all smoke tests before traffic cutover
- Health check passes on all green instances
- Database connection pool established and stable
- Rollback path (revert DNS/load balancer to blue) confirmed accessible

**Rolling deployment verification:**
- Each batch of instances passes health check before proceeding to next batch
- Error rate does not increase during rolling update (monitored between batches)
- No batch exceeds 25% of fleet in a single update step

**Direct-full deployment verification:**
- Deployment completes with no instance failures
- Health check passes on all instances post-deployment
- Smoke test suite passes

---

## 3. Exposure Standards

### Progressive Delivery Requirements

| Tier | Progressive Exposure Required? | Conditions |
|------|-------------------------------|-----------|
| 1 | Yes | No exceptions. All Tier 1 changes require at least two stages before full exposure. |
| 2 | Yes | Exception: direct-full permitted if the change is a pure configuration update (no code change) OR the change has already been at full exposure in a non-production environment for 7+ days with zero incidents. Exception must be documented in RP §3. |
| 3 | No | Progressive delivery recommended but not required. |

### Rollout Constraints

- **Maximum exposure increment per stage:** 25 percentage points — no single stage may increase exposure by more than 25 percentage points
- **Minimum observation period between stages:** 4 hours — the minimum time that must elapse, with the release owner actively reviewing metrics, before proceeding to the next stage
- **Direct-full exposure permitted for Tier 1:** No — not permitted under any conditions

### Feature Flag Governance

- **Flag lifecycle stages:** inactive → percentage rollout → segment targeting → full exposure → removed
- **Cleanup date requirement:** Flags must be removed within 30 days of reaching full exposure
- **Maximum simultaneous active flags per system:** 5 — to prevent interaction effects; flags at "inactive" do not count against this limit
- **Flag naming convention:** `{FEATURE}_{BEHAVIOR}` in all caps with underscores (e.g., `NOTIF_ENABLED`, `NOTIF_BATCH_SIZE`)

---

## 4. Release Authorization Model

### Authorization Levels

| Level | Role / Title | Authorized Up To | Escalation Required Above |
|-------|-------------|-----------------|--------------------------|
| 1 | Engineer | 10% exposure | 10% |
| 2 | Senior Engineer / Tech Lead | 50% exposure | 50% |
| 3 | Engineering Manager | 100% / full release | — |

### Escalation Triggers

The following conditions require escalating to the next authorization level:
- Any rollback event, regardless of exposure percentage
- Tier 1 system exposure above 10% (requires Level 3 authorization)
- Any alert that fires and is not resolved within the decision window
- Customer-impacting incidents during the release window
- Changes that affect more than one team's systems (cross-boundary)

### Emergency Release Procedure

For releases that cannot follow normal planning steps (e.g., critical security patch):

1. Engineering Manager or above authorizes the emergency release in writing (Slack message with timestamp to #releases channel is acceptable)
2. Release owner completes a minimal Release Entry Record documenting the ORD ID and authorization reference
3. Release is executed with monitoring active; rollback must be possible before proceeding
4. Full Release Plan and Release Record must be completed within 48 hours of the emergency release completing

_Emergency releases require a named release owner and a documented rollback procedure. Emergency authorization does not waive accountability._

### Cross-Boundary Releases

For releases that affect multiple teams or systems: the release owners from each affected team must jointly complete the Release Entry Record. The most restrictive authorization level applies (i.e., if one system is Tier 1, the entire release is governed as Tier 1).

---

## 5. Communication Requirements

### Internal Stakeholder Notifications

| Audience | Notification Stage | Content | Channel |
|----------|-------------------|---------| --------|
| Product Management | 24 hours before release window opens | What is being released, release window, expected user impact | Email |
| Engineering team | 1 hour before execution begins | Release start, who is the release owner, rollback contact | #releases Slack channel |
| Engineering team | At each exposure stage transition | Current exposure %, signals observed, decision made | #releases Slack channel |
| Engineering team | At release completion or rollback | Outcome, final exposure state, any incidents | #releases Slack channel |
| Customer Success | 24 hours before release window (if customer-notification trigger met) | What change is coming, when, who to contact with questions | Email |

### Customer-Facing Communication Triggers

Customer notification is required when the release involves any of the following:
- A visible behavior change to the TaskFlow UI that users will notice without context
- A new feature that changes default settings or notification behavior
- A performance degradation of more than 10% on any user-visible operation
- An API breaking change affecting API consumers
- A data migration that requires user action

### Partner / API Consumer Notification

TaskFlow exposes a public REST API. API consumers must be notified at least 5 business days before any breaking change (endpoint removal, field removal, behavior change). Non-breaking changes (new optional fields, new endpoints) do not require advance notice but should be documented in the API changelog.

---

## 6. Monitoring Requirements

### Watch Period

- **Minimum watch period per exposure stage:** 4 hours — the minimum time the release owner must actively monitor metrics before closing a stage

### Required Metrics

The following metric categories must be monitored for every release (Release Plans must identify specific metrics within each category):

| Category | Description |
|----------|-------------|
| Error rate | Application error rate (5xx responses) and client-side JavaScript error rate per page |
| Latency | p50, p95, p99 response time for all API endpoints and page load time |
| Business metric | Feature-specific engagement or completion rate (e.g., notification delivery rate, feature activation rate) |
| Infrastructure | CPU utilization, memory usage, database query latency |

### Alert Threshold Policy

- **Default policy:** Thresholds must be relative to an established baseline (percentage deviation or absolute value comparison to pre-release baseline)
- **When no baseline exists:** For first-time releases of a new feature, set absolute thresholds based on system-wide targets (e.g., error rate <0.5%, p99 latency <500ms) and document them as "first-release targets"
- **Threshold tightening for Tier 1:** Tier 1 systems must use thresholds 50% tighter than baseline deviation (e.g., if standard threshold is 2x baseline, Tier 1 uses 1.5x baseline)

### SLO Baseline Documentation

- **When to capture:** Before release begins — from ORD evidence or from monitoring system export
- **How to store:** In the Release Plan §6 (baseline values) and archived in the team's release records folder
- **Retention:** Baselines must be retained for 12 months minimum

---

## 7. Scope and Exceptions

### Scope

This RCF governs: all production services in the TaskFlow SaaS platform, including backend services, frontend web applications, mobile API endpoints, and data pipeline jobs owned by the TaskFlow engineering organization.

Explicitly excluded: third-party integrations not owned by TaskFlow (Stripe, SendGrid, etc.); infrastructure managed by the platform team under a separate RCF; releases to staging and pre-production environments (no RCF required for non-production).

### Documented Exceptions

| Exception | Affected Standard | Justification | Expiry Condition |
|-----------|-----------------|---------------|-----------------|
| Mobile app releases do not require a Release Entry Record | §3 entry gate | Mobile app store releases follow a separate approval process controlled by Apple/Google; the standard release entry record doesn't map to app store submissions | Review by end of Q2 2024 — if mobile native apps are added to scope, update this exception |

---

## Freeze Declaration

| Field | Value |
|-------|-------|
| Frozen | Yes |
| Freeze Date | 2024-02-01 |
| Approved By | David Park (Engineering Manager) |
| Notes | Version 1.0 — covers all TaskFlow production services. Mobile app exception documented in §7. |
