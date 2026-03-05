# Release Plan — Specification

The Release Plan (RP) is the initiative-specific artifact that defines how a specific system or feature will be released to production. It translates the operational readiness evidence from the ORD and the organizational standards from the RCF into a concrete, executable release strategy.

The RP is **per-release** — it is generated for each release engagement and is not reused.

---

## Purpose

The Release Plan defines:
- Which deployment strategy will be used and what the exact steps are
- How users will be progressively exposed (stages, targets, triggers)
- What conditions trigger a rollback and exactly what the rollback procedure is
- Who must be notified and when
- What metrics will be watched, for how long, and what thresholds signal success or failure

A frozen RP, combined with a completed Pre-Release Authorization Checklist, is the authorization to begin release execution.

---

## Upstream Dependencies

- Frozen ORD (confirmed in Release Entry Record) — provides runbook procedures and operational evidence baselines
- Frozen RCF — provides organizational standards that constrain the plan

---

## Required Sections

1. Document Control
2. Deployment Specification
3. Exposure Specification
4. Rollback Specification
5. Communication Plan
6. Post-Release Monitoring Plan
7. Open Issues
8. Pre-Release Authorization Checklist

---

## Content Rules

### Document Control
**Rules**
- RP ID must be present (format: RP-{PROJECT}-{NNN})
- ORD ID and RCF ID must be referenced with their frozen status confirmed
- Release summary must be present (1-2 sentences describing what is being released)
- Status must be one of: Draft | Validated | Frozen

**Failure Examples**
- ORD or RCF ID missing
- ORD or RCF status listed as Draft or Approved rather than Frozen
- Release summary absent

### Deployment Specification
**Rules**
- Deployment strategy must be selected from the strategies permitted by the RCF for the system's risk tier
- Steps must be concrete and deterministic — each step states exactly what action is performed and by whom or what system
- Each step must reference the relevant ORD runbook section (e.g., "ORD §8 Deploy procedure, step 3")
- Verification steps must be defined: after each significant deployment step, state how success is confirmed and what evidence is captured
- Timeline must be stated: expected duration for each phase and total deployment window
- Configuration changes (environment variables, secrets, feature flag states) must be listed explicitly

**Failure Examples**
- "Deploy as described in ORD" — not a specification; steps must be enumerated in the RP
- Verification steps absent
- No reference to ORD runbook sections
- Configuration changes not listed

### Exposure Specification
**Rules**
- At least two exposure stages must be defined (the first partial stage and full exposure); direct-full exposure requires explicit justification documented in the RP that references the RCF exception criteria
- Each stage must specify: target exposure (percentage or audience segment), trigger to proceed to next stage (observable signal with threshold), and what action is taken if the trigger is not met (wait longer, investigate, pause)
- Stage targets must comply with the RCF maximum exposure increment
- Stage timing must comply with the RCF minimum observation period
- Feature flag configuration for each stage must be stated: flag name, value at each stage, and who sets it
- The audience targeting logic for segment-based rollout must be defined as evaluable criteria

**Failure Examples**
- Only one stage defined (full exposure) without exception justification
- Trigger to proceed is "team decides" rather than an observable signal with threshold
- Feature flag names absent or "TBD"
- Stage increments exceed RCF maximum without documented exception

### Rollback Specification
**Rules**
- Rollback conditions must be stated as concrete, observable signals with defined thresholds (e.g., "error rate exceeds 0.5% sustained for 5 minutes" or "p99 latency exceeds 500ms at any exposure stage")
- At least two rollback conditions must be defined (error signal and latency/performance signal at minimum)
- The rollback procedure must reference the ORD §8 rollback procedure explicitly
- The responsible party for triggering rollback must be named (a person or role, not "the team")
- The decision authority must be specified: who can authorize a rollback decision and who escalates if that person is unavailable
- The maximum time window for making a rollback decision after a condition is triggered must be stated

**Failure Examples**
- "Rollback if things look bad" — not a condition
- Only one condition defined
- No reference to ORD rollback procedure
- Responsible party is "the team" or unnamed
- Decision window absent

### Communication Plan
**Rules**
- Internal communication requirements must cover all audiences required by the RCF, with what is communicated, at what release event, and in what channel
- Customer-facing communication must be defined if the release meets any RCF customer-notification trigger; if no triggers are met, state that explicitly with the trigger criteria referenced
- Partner or API consumer communication must be defined or explicitly marked not applicable with justification
- Each communication must state: audience, content summary, timing (before release / at start / at each stage / at completion), channel, and owner

**Failure Examples**
- Internal communication covers only engineering, omitting product/stakeholders required by RCF
- Customer communication absent when the release meets an RCF trigger
- "Send a Slack message" — not a complete communication plan (to whom, what content, when)
- Timing of communication not specified relative to release events

### Post-Release Monitoring Plan
**Rules**
- Specific metrics must be named (not metric categories — the actual metric names as they appear in the monitoring system)
- Baseline values for each metric must be stated (sourced from ORD evidence or explicitly documented as "baseline not yet established — first release")
- Alert thresholds must be defined as deviations from baseline or absolute values consistent with the RCF alert threshold policy
- The watch period must be stated as a clock duration and must meet the RCF minimum watch period for each exposure stage
- Check-in schedule must be defined: how often the release owner or designated monitor reviews metrics during the watch period
- Success criteria must be stated: what signals confirm the stage is stable and expansion can proceed

**Failure Examples**
- "Monitor error rates and latency" — not named metrics
- Baseline values absent
- Watch period stated as "until stable" rather than a duration
- Check-in schedule absent
- Alert thresholds defined without reference to baseline or RCF policy

### Open Issues
**Rules**
- Any unresolved decision or dependency that could affect release execution must be listed
- Each open issue must include: description, owner, resolution deadline, and "Blocks Release?" status
- Open issues that block release may not remain open when the RP is frozen — they must be resolved or converted to explicit scope exclusions
- If no open issues exist, state "None" — an empty section is a failure

**Failure Examples**
- Section left empty (without "None")
- Open issues present that block release
- Issues listed without owner or resolution deadline

### Pre-Release Authorization Checklist
The Pre-Release Authorization Checklist is completed by the release owner immediately before execution begins. It is not evaluated by the RP validator — it is the authorization gate that separates plan approval from execution authorization.

**Required checklist items:**
- [ ] ORD status confirmed Frozen (re-verified at execution time)
- [ ] RP validated (PASS) and frozen
- [ ] Feature flags configured per RP §3 for initial stage
- [ ] Rollback procedure reviewed and confirmed operational (ORD runbook tested)
- [ ] Monitoring dashboards active; baseline values visible
- [ ] Automated alerts configured per RP §6
- [ ] Internal communications sent per RP §5 (pre-release notifications)
- [ ] Release window confirmed with stakeholders
- [ ] Release owner confirms authorization to proceed

**Rules for the checklist:**
- The checklist must be present in the template and left blank in the frozen RP (it is completed at execution time, not before freeze)
- When the checklist is completed, the release owner's signature and date must be added
- If any checklist item cannot be confirmed, the release must not proceed until it is resolved

---

## Format Requirements

- Rollback conditions must use observable, measurable language — not qualitative assessment
- Metric names must match the actual names in the monitoring system — not categories
- Durations must be expressed as clock time — not "short period" or "adequate time"
- ORD references must cite the specific section (e.g., "ORD §8 Deploy procedure") — not just "ORD"

---

## Completeness Rules

- All eight sections must be present and non-empty (Open Issues may contain "None")
- At least two exposure stages defined
- At least two rollback conditions defined
- All RCF communication requirements addressed
- Named metrics with baseline values
- Watch period meets RCF minimum

---

## Relationship Rules

- The RP is a downstream artifact of both the ORD and the RCF — it must comply with RCF standards and reference ORD runbook procedures
- The RP is the upstream input to the Release Record — the RR documents what actually happened against what the RP planned
- Deviations between RP and RR must be documented in the RR (not silently carried in the RP)
- The RP may not be modified after the Pre-Release Authorization Checklist is completed — changes discovered during execution are documented in the RR

---

## Hard Gates

1. **upstream_references** — RP ID, ORD ID (Frozen status confirmed), RCF ID (Frozen status confirmed), release summary all present
2. **deployment_specification** — Strategy from RCF-permitted list; concrete deterministic steps with ORD runbook references; verification steps after each major step; configuration changes listed
3. **exposure_specification** — At least two stages (or justified exception); each stage has target, proceed trigger as observable signal with threshold, and hold action; RCF increment and observation period complied with; flag configuration per stage stated
4. **rollback_specification** — At least two conditions as observable signals with thresholds; procedure references ORD §8 rollback; named responsible party; decision authority and time window specified
5. **communication_plan** — All RCF-required audiences covered with content, timing, channel, owner; customer notification addressed (or explicitly exempted with trigger reference)
6. **monitoring_plan** — Named metrics (not categories); baseline values; alert thresholds per RCF policy; watch period as duration meeting RCF minimum; check-in schedule; success criteria
7. **scope_bounded** — RP scope matches RER declaration; no expansion beyond what the RER states
8. **readiness** — Open Issues section present; no issues marked "Blocks Release?"; plan is deterministic (no remaining design decisions)
