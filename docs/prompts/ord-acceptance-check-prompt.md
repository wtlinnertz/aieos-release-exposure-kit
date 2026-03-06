# ORD Acceptance Check — Utility Prompt

## Purpose

Verify that the Release Entry Record (RER) accurately reflects the content and state of the frozen Operational Readiness Document (ORD) it gates against. This check runs at the REK entry boundary to confirm the RER's claims about the ORD are accurate before release planning begins.

This is a **utility prompt** — it produces analysis for human review. Its output does not substitute for the Release Entry Validator, which evaluates the RER against its spec.

## When to Use

Run this prompt after the RER has been drafted and before running the Release Entry Validator — to confirm the RER's statements about the ORD are accurate and complete.

Specifically useful when:
- The ORD was authored by a different team than the release team
- Time has passed between ORD freeze and release engagement start
- The release scope is a subset of the full ORD scope

## Inputs Required

1. The frozen ORD from the Engineering Execution Kit
2. The drafted Release Entry Record (RER)

## Instructions

### Step 1: Production Readiness Claim Verification

The RER declares the ORD's production readiness state. Cross-reference this declaration against the ORD:

1. Confirm the ORD cited in the RER matches the actual ORD provided (ID and status).
2. Confirm the ORD status is `Frozen` — if the RER cites a non-frozen ORD, flag this as a blocking issue.
3. Confirm the RER's production readiness summary accurately reflects the ORD's overall readiness verdict. Look for the ORD's final readiness declaration and confirm the RER does not overstate or understate it.

### Step 2: Monitoring System Verification

The ORD documents monitoring and observability requirements. Confirm that:

1. Each monitoring system or observability requirement stated in the ORD (typically in the operational requirements section) is acknowledged in the RER.
2. The RER does not silently omit monitoring requirements that the ORD identified as mandatory for production operation.
3. If the RER's scope is a subset of the ORD scope, confirm that the in-scope portions of monitoring are accurately represented.

### Step 3: Open Items and Conditions Check

Review the ORD for:
1. Any open items, deferred requirements, or conditions that were documented but not yet resolved at ORD freeze.
2. Confirm whether these items appear in the RER — either resolved, acknowledged, or explicitly accepted with rationale.
3. Flag any open items from the ORD that the RER does not address.

### Step 4: Release Scope Consistency

Confirm that the release scope stated in the RER is consistent with what the ORD covers:
1. If the RER claims to release a subset of what the ORD covers, confirm the subset is clearly defined.
2. Flag if the RER's stated release scope includes functionality that the ORD does not declare production-ready.

### Step 5: Verdict

Render one of:
- `PASS` — RER accurately reflects ORD production readiness; no monitoring requirements omitted; no silent drops
- `FAIL` — Discrepancies found (list each with location and severity)
- `WARN` — Minor gaps that do not block release but require human review

## Output Format

```
## ORD Acceptance Check

**Artifacts reviewed:**
- ORD: {ORD-ID} — Status: {status}
- RER: {RER-ID}

**Verdict: PASS / FAIL / WARN**

### Findings

| Finding Type | Severity | ORD Location | RER Location | Description |
|-------------|---------|-------------|-------------|-------------|
| {Omission / Mismatch / Silent drop / Open item not addressed} | {Blocking / Warning} | {ORD §N} | {RER §N or "absent"} | {Description of the discrepancy} |

*If no findings: "No discrepancies detected."*

### Monitoring Coverage Summary

| ORD Monitoring Requirement | Reflected in RER? | Notes |
|--------------------------|-----------------|-------|
| {monitoring system or requirement} | Yes / No / Partial | {notes if partial or absent} |

### Summary

{1–3 sentences describing the overall acceptance state. If FAIL, state what must be resolved before the RER can be frozen. If WARN, state what the release team should review before proceeding.}
```

## Behavioral Rules

- Do not evaluate whether the ORD content is sufficient — evaluate whether the RER accurately reflects what the ORD contains.
- Do not suggest changes to the ORD — it is frozen and must not be modified to satisfy the RER.
- If a monitoring requirement is present in the ORD but absent in the RER, always flag it — the release team decides whether to address it or accept the gap.
- Base analysis only on what is explicitly stated in both documents — do not infer intent from context.
- Do not assess whether the ORD's production readiness verdict is correct — only whether the RER reflects it accurately.
