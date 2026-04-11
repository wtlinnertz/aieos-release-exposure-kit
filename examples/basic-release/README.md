# Basic Release Example — TaskFlow Notifications

This example walks through a complete Release & Exposure Kit flow for the TaskFlow Notifications feature release. It demonstrates the happy path: a canary release that progresses through three exposure stages and reaches full exposure without incident.

---

## Scenario Context

**Feature:** TaskFlow Notifications — real-time push notifications for task assignments, comments, and status changes.

**Upstream ORD:** ORD-TASKFLOW-001 (frozen) — produced by the Engineering Execution Kit after the notification service, delivery infrastructure, and frontend component were built, tested, and verified operationally ready.

**Scenario thread:** This is the same TaskFlow project used in the Product Intelligence Kit examples (discovery flow) and the Engineering Execution Kit examples (build and test flow). This example picks up where the EEK leaves off: the ORD is frozen, the system is operationally ready, and the release team is preparing to expose the feature to production users.

---

## Files in This Example

| File | Artifact | Description |
|------|----------|-------------|
| `00-release-entry.md` | Release Entry Record (RER-TASKFLOW-001) | Entry gate confirming ORD is frozen, release scope declared, owner named, authorization on record |
| `01-rcf.md` | Release Context File (RCF-TASKFLOW-001) | TaskFlow org release policy — risk tiers, exposure standards, authorization model, communication requirements, monitoring requirements |
| `02-rp.md` | Release Plan (RP-TASKFLOW-001) | Canary release plan: 10% → 25% → 100%; flag configuration; rollback conditions; monitoring plan |
| `03-rr.md` | Release Record (RR-TASKFLOW-001) | Evidence of what happened: deployment logs, exposure decisions, metric observations, final disposition, Layer 6 handoff |
| `validator-outputs/` | Validation results | PASS JSON for all 4 artifacts |

---

## Release Flow Walkthrough

### Step 0: Release Entry Gate

Sarah Chen (Senior Engineer, release owner) completes `00-release-entry.md`:
- Confirms ORD-TASKFLOW-001 is in Frozen status
- Declares the release scope (notifications feature, canary type, 10% initial target)
- Records her authorization level (up to 50% without escalation; above 50% requires David Park, Engineering Manager)
- References the Q1 planning session authorization (JIRA record SPR-2024-Q1-R03)

Validator output: **PASS** — one warning noting that full exposure (100%) will require manager escalation per the authorization level. This is anticipated and handled in the RP.

### Step 1: Reusing the Existing RCF

RCF-TASKFLOW-001 was authored by the Platform Engineering team in February 2024 and covers all TaskFlow production services. Sarah confirms it applies to this release and reuses it — no new RCF generation needed.

The RCF establishes:
- **Tier classification:** TaskFlow Notifications is Tier 2 (user-facing feature, no authentication or payment handling)
- **Maximum increment:** 25 percentage points per stage
- **Minimum observation period:** 4 hours per stage
- **Authorization:** Sarah can authorize up to 50%; Engineering Manager authorizes up to 100%
- **Flag governance:** Flags must be removed within 30 days of full exposure

Validator output: **PASS** — two warnings: (1) mobile app exception review deadline approaching, (2) Stage 3 increment exceeds 25% limit (exception pathway exists in RCF for Tier 2; must be justified in RP).

### Step 2: Release Plan

The RP defines a three-stage canary:
- **Stage 1 (10%):** 4-hour observation; proceed if error rate <0.1% and p99 <300ms
- **Stage 2 (25%):** 4-hour observation; same success criteria plus notification delivery rate >98%
- **Stage 3 (100%):** 4-hour watch period; complete

**Stage 3 increment exception:** The jump from 25% to 100% is 75 percentage points — exceeding the RCF 25-point maximum. This exception is justified in RP §3: the notification feature was at full exposure in staging for 14 days with zero incidents, meeting the RCF Tier 2 exception criteria. Exception authorized by David Park.

**Feature flags:**
- `NOTIF_ENABLED`: 10% → 25% → 100%
- `NOTIF_BATCH_SIZE`: 50 (Stage 1-2) → 100 (Stage 3)

**Rollback conditions:** Error rate >0.5% for 5 minutes OR p99 >500ms (any measurement) OR delivery rate <95% for 15 minutes. Decision window: 15 minutes.

**Key design decision in the RP:** The Pre-Release Authorization Checklist (§8) is shown completed in this example to illustrate what a complete checklist looks like at execution time. In normal practice, it is left blank at freeze and completed immediately before execution begins.

Validator output: **PASS** — warnings: (1) Stage 3 exception noted (authorized in RP), (2) no baseline for notification.delivery_rate (first release, correctly documented as absolute threshold), (3) checklist appears pre-filled (example artifact).

### Step 3: Release Execution

Execution proceeded over three days:

**Day 1 (2024-03-18):**
- 10:03–10:41 UTC: Deployment steps complete (all 5 steps, no deviations)
- 10:22–14:22 UTC: Stage 1 watch period — error rate 0.02%, p99 195ms, delivery rate 99.1%
- 14:22 UTC: Sarah decides to proceed to Stage 2 (all criteria met)
- 15:00 UTC: NOTIF_ENABLED updated to 25%
- 15:00–19:00 UTC: Stage 2 watch period — metrics stable, delivery rate improved to 99.3%
- 20:00 UTC: NOTIF_ENABLED updated to 100%; NOTIF_BATCH_SIZE updated to 100; David Park authorizes final expansion

**Day 2-3 (2024-03-19–21):**
- Extended watch at 100% — watch period runs to next business day for full coverage
- No alerts fired; all metrics within thresholds
- 2024-03-21 09:00 UTC: Sarah declares release complete

### Step 4: Release Record

The RR documents the full 70.5-hour release with concrete evidence at each step:
- Deployment logs and LaunchDarkly audit records for all 5 deployment steps
- Observed metric values with timestamps at each exposure stage
- No deviations from the RP (no pauses, no investigations, no alerts)
- Full disposition: successful-full-exposure at 100%

**Layer 6 handoff** (RR §7) includes:
- What's in production: notification-service v1.4.0, notification-ui-component v2.1.0
- All alerts active in Datadog
- SLO baseline from ORD (error rate, latency) plus first-release notification delivery rate baseline
- Three Layer 6 watch items: delivery rate baseline establishment, CPU trajectory, flag cleanup deadline

Validator output: **PASS** — two warnings: (1) extended Stage 3 watch period not explained in RR (minor auditability gap), (2) delivery rate SLO baseline from Stage 1 should be validated by Layer 6.

---

## Key Observations from This Example

**1. The RCF is reusable.** Sarah reused RCF-TASKFLOW-001 rather than generating a new one. The RCF was authored once by the platform team and applies to all TaskFlow services.

**2. Exceptions are documented, not hidden.** The Stage 3 increment exception (75 points > 25-point limit) is explicitly justified in RP §3 with a manager authorization reference. The validator flags it as a warning — confirming the exception is present and noted.

**3. Rollback was never needed, but the conditions were defined.** The rollback conditions (observable signals, thresholds, decision window) were ready before execution began. The RR documents that no conditions were triggered.

**4. The handoff to Layer 6 is the final artifact.** The RR §7 provides everything Layer 6 needs to begin oversight: what's running, at what version, what monitoring is active, and what deserves elevated attention.

**5. The Pre-Release Authorization Checklist is the go/no-go gate.** Sarah completed all 10 items before execution and signed off at 10:00 UTC on release day. This is the human decision that authorizes execution — not the RP freeze date.
