# Troubleshooting Guide — Release Exposure Kit

## How to Use This Guide

When a validator returns FAIL, find the failing gate in the table below. The Remediation column describes the specific fix required. Reopen the artifact, apply the remediation, and rerun the validator in a new session.

**Do not embed fix attempts in your validation session.** Validators and generation are separate sessions.

---

## Release Entry Record (RER-{PROJECT}-{NNN})

This is a human-completed entry gate artifact. Fill all fields directly in the template before running the validator.

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing ID, date, release owner, or status | Template not fully completed | Fill all §1 fields including release owner by name; no field may be blank |
| upstream_reference | No ORD ID or reference to upstream ORD | ORD not linked | Record the frozen ORD ID and its location in the consuming project |
| release_scope | Release scope not bounded | Scope assumed from ORD without being stated | State explicitly: which components, which users, and which environments are included |
| release_owner | Release owner not named as an individual | Owner left as a team name or role | Name the specific individual who is personally accountable for this release |
| priority_confirmed | Priority not confirmed | Priority assumed without stakeholder confirmation | Record the priority classification and name the stakeholder who confirmed it |

---

## Release Context File (RCF-{ORG}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| document_control | Missing ID, owner, version, or scope | Template fields not filled | Complete §1 including org scope (org-wide vs. team-specific); all fields required |
| deployment_standards | Standards aspirational ("use canary when appropriate") | Policy written for guidance rather than governance | Define: when each deployment type is required, the specific criteria that trigger each type, and any percentage thresholds |
| exposure_standards | No feature flag or traffic split criteria defined | Exposure policy absent | Define: what percentage thresholds constitute each exposure stage, and the pass/fail criteria for advancing between stages |
| authorization_model | Who can approve releases not defined | Authorization left to team convention | Define: who approves each release type and what constitutes a valid approval |
| communication_requirements | Communication plan not defined | Communication assumed from team norms | State: what must be communicated, to whom, and at which release stage |
| monitoring_requirements | Monitoring requirements during release not defined | Monitoring assumed from ORD | Define: which metrics to watch, the thresholds that indicate a problem, and the monitoring window duration |
| standard_enforceability | Standards written aspirationally | Written for guidance, not governance | Replace aspirational language with specific, testable criteria throughout |

---

## Release Plan (RP-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| upstream_references | ORD or RCF not referenced in §1 | Plan generated without confirming frozen inputs | Record the frozen ORD ID and RCF ID explicitly in §1 |
| deployment_specification | Deployment steps incomplete or vague | Steps copied from ORD without verification | Define exact steps, their order, the executor for each step, and the verification criterion that confirms each step succeeded |
| exposure_specification | Exposure stages not defined | Exposure plan absent | Define each exposure stage: target percentage, pass criteria, stage duration, and who evaluates the criteria |
| rollback_specification | Rollback trigger not defined as an observable signal | Trigger stated as "if things go wrong" or "team decides" | Specify the exact metric or signal that triggers rollback, the numeric threshold, and the rollback steps to execute |
| communication_plan | Stakeholder communication not planned | Communication assumed | Define: who is notified at each stage, how they are notified, and what the message contains |
| monitoring_plan | Monitoring window and signals not defined | Monitoring assumed from ORD or RCF | Specify: what is monitored, the thresholds, and the duration of heightened monitoring during the release |
| scope_bounded | RP scope adds items not present in RER | Scope expansion between RER and RP | Align RP scope with RER §2 exactly; additions require a new RER |
| readiness | Readiness criteria not explicitly confirmed | Readiness checklist not reviewed before freeze | Complete the readiness checklist; each item must be explicitly confirmed |

---

## Release Record (RR-{PROJECT}-{NNN})

| Gate | What Failure Looks Like | Typical Cause | Remediation |
|------|------------------------|---------------|-------------|
| release_summary | Summary contradicts RP scope or outcome | Drift from the plan during documentation | Align summary with RP §2; note deviations from plan explicitly with explanation |
| deployment_evidence | Deployment steps documented as assertions only | Evidence not gathered during execution | Replace assertions with retrievable evidence: log reference, timestamp, or CI run ID |
| exposure_log | Exposure stages not logged | Stages assumed to have completed without documentation | Record each exposure stage outcome: the percentage reached, the duration, and the pass criteria result |
| monitoring_observations | Monitoring section blank | Monitoring observations not recorded during release window | Record key metrics observed during the release window; state what was normal and what, if anything, was abnormal |
| release_disposition | Disposition not from the enumerated values | Free-text disposition written | Use exactly one of: Released / Rolled Back / Held — with written rationale |
| handoff_declaration | §7 Handoff to Layer 6 incomplete or absent | Layer 6 handoff not considered during RR completion | Complete §7 with: service name, SLO baseline values, known failure modes, current monitoring state, and reliability owner |
