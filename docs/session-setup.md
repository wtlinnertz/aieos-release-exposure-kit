# Session Setup — Release & Exposure Kit

Use this file to set up an AI session for each REK artifact. Find the section for the artifact you are generating or validating. Follow the checklist before starting.

**Rule:** Generate and validate in separate sessions. Do not self-validate.

---

## Release Entry Record (RER-{PROJECT}-{NNN})

**What you're creating:** The formal gate that authorizes REK work to begin, confirming the upstream ORD is frozen and naming a specific release owner.

**Note:** The RER is human-authored — no generation prompt. Complete it directly using the template.

**Required Inputs (confirm before starting):**
- [ ] Frozen ORD (ORD-{PROJECT}-{NNN}) — Frozen and received from EEK?
- [ ] Named release owner — Identified (specific individual, not a team)?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `document_control`: ID, date, release owner (named individual), and status ready
- [ ] `upstream_reference`: Frozen ORD ID on hand to record
- [ ] `release_scope`: Which components, users, and environments are in scope — known and bounded
- [ ] `release_owner`: Specific individual named and accountable
- [ ] `priority_confirmed`: Priority classification and confirming stakeholder known

**Session Setup:**
1. Use: `docs/artifacts/release-entry-template.md` — fill manually, no prompt
2. No validator — human review and freeze
3. After RER is frozen, check for an existing RCF before generating a new one

**Common Failure to Avoid:**
Leaving release owner as a team name or role — the RER requires a specific named individual accountable for this release.

---

## Release Context File (RCF-{ORG}-{NNN})

**What you're creating:** The organizational release policy — deployment standards, exposure rules, authorization model, and communication requirements. Org-level; reuse across releases if unchanged.

**Check first:** Does a frozen RCF already exist for your organization? If yes, do not generate a new one — use the existing one. Only generate when no RCF exists or when organizational policy has changed.

**Required Inputs (confirm before starting):**
- [ ] Organizational release policy (human input) — Documented?
- [ ] Principles files — Available in `docs/principles/`?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `document_control`: ID, owner, version, and scope (org-wide vs. team-specific) planned
- [ ] `deployment_standards`: Specific criteria for when each deployment type is required — known
- [ ] `exposure_standards`: Feature exposure percentage thresholds and pass/fail criteria — defined
- [ ] `authorization_model`: Who approves each release type — defined (named roles or individuals)
- [ ] `communication_requirements`: What, to whom, at what stage — defined
- [ ] `monitoring_requirements`: Metrics to watch, thresholds, monitoring duration — defined
- [ ] `standard_enforceability`: All standards will be testable, not aspirational

**Session Setup:**
1. Load: `docs/prompts/rcf-prompt.md`
2. Provide: Organizational release policy documentation (standards, procedures, approval workflows)
3. Provide: `docs/specs/rcf-spec.md` (or confirm it is in context)
4. Validate in a separate session: `docs/validators/rcf-validator.md`

**Common Failure to Avoid:**
Standards written aspirationally ("use canary when appropriate") — define specific, testable criteria: when exactly each deployment type is required, what the threshold is, and who evaluates it.

---

## Release Plan (RP-{PROJECT}-{NNN})

**What you're creating:** A per-release execution plan — deployment steps, exposure stages, rollback triggers, communication plan, and monitoring plan.

**Required Inputs (confirm before starting):**
- [ ] Frozen ORD — Frozen?
- [ ] Frozen RCF — Frozen?
- [ ] Frozen RER — Frozen?

**Pre-Flight Gate Check (verify before generating):**
- [ ] `upstream_references`: Frozen ORD ID and RCF ID on hand to record in §1
- [ ] `deployment_specification`: Exact steps, order, executor, and verification criterion are plannable
- [ ] `exposure_specification`: Exposure stages, target percentages, pass criteria, and durations defined
- [ ] `rollback_specification`: Observable rollback trigger (specific metric + threshold) identified
- [ ] `communication_plan`: Who is notified, when, and how — planned
- [ ] `monitoring_plan`: What metrics, thresholds, and heightened monitoring window — defined
- [ ] `scope_bounded`: RP scope matches RER §2 exactly — no additions
- [ ] `readiness`: All readiness criteria are satisfiable before the planned release date

**Session Setup:**
1. Load: `docs/prompts/rp-prompt.md`
2. Provide: Frozen ORD, frozen RCF, and frozen RER
3. Provide: `docs/specs/rp-spec.md` (or confirm it is in context)
4. Validate in a separate session: `docs/validators/rp-validator.md`

**Common Failure to Avoid:**
Rollback trigger stated as "team decides" or "if things go wrong" — define as an observable signal: specific metric name, threshold value, and time window that triggers rollback.

---

## Release Record (RR-{PROJECT}-{NNN})

**What you're creating:** The evidence record of the actual release — what was deployed, what exposure was reached, what was observed, and the final disposition. Generated after the release completes (or is rolled back).

**Note:** The RR is generated after the release executes. Do not generate it in advance.

**Required Inputs (confirm before starting):**
- [ ] Frozen RP — Frozen?
- [ ] Actual release execution evidence — Available? (deployment logs, exposure metrics, monitoring data)
- [ ] Release disposition decision — Made? (Released / Rolled Back / Held)

**Pre-Flight Gate Check (verify before generating):**
- [ ] `release_summary`: Can summarize what was released and any deviations from RP
- [ ] `deployment_evidence`: Retrievable evidence for each deployment step (log, timestamp, CI run ID)
- [ ] `exposure_log`: Each exposure stage outcome logged (percentage reached, duration, pass criteria)
- [ ] `monitoring_observations`: Key metrics observed during the release window are documented
- [ ] `release_disposition`: Disposition is from the enumerated list: Released / Rolled Back / Held
- [ ] `handoff_declaration`: §7 Handoff to Layer 6 fields are completable (service name, SLO baseline, failure modes, monitoring state, reliability owner)

**Session Setup:**
1. Load: `docs/prompts/rr-prompt.md`
2. Provide: Frozen RP (as the plan to compare against)
3. Provide: All release execution evidence (deployment records, exposure data, monitoring observations)
4. Provide: `docs/specs/rr-spec.md` (or confirm it is in context)
5. Validate in a separate session: `docs/validators/rr-validator.md`
6. After PASS: complete §7 Handoff to Layer 6 and deliver to RRK

**Common Failure to Avoid:**
Deployment step evidence as assertion-only ("deployment succeeded") — replace with retrievable evidence: log reference, timestamp, CI run ID, or equivalent traceable record.
