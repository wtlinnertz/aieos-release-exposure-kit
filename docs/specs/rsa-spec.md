# Release Safety Assessment — Specification

Version: v1.0

The Release Safety Assessment (RSA) aggregates risk evidence from upstream kits into a single structured safety brief before release planning. It consolidates quality, security, and deployment risk signals into an actionable risk profile that informs the Release Plan.

---

## What This Artifact Is Not

- **Not a risk register.** The RSA is a pre-release aggregation of evidence from upstream artifacts, not a comprehensive enterprise risk management document.
- **Not a security assessment.** The RSA references SCK artifacts (TM, SAR) when available; it does not perform its own security analysis.
- **Not a quality report.** The RSA references QAK artifacts (QGR) when available; it does not re-evaluate test results.

---

## Purpose

The RSA serves three roles:

1. **Risk aggregation** — Consolidates risk signals from upstream kits (QAK, SCK, EEK) into a single view
2. **Safeguard recommendation** — Translates aggregated risk into concrete release safeguards (slower rollout, additional monitoring, rollback triggers)
3. **Residual risk documentation** — Makes explicit what risks remain after safeguards, requiring conscious acceptance before release planning proceeds

---

## Upstream Dependencies

- Frozen RER (mandatory) — confirms release scope and authorization
- Frozen RCF (mandatory) — confirms organizational release standards
- Frozen QGR (optional, when QAK adopted) — quality gate results and findings
- Frozen TM (optional, when SCK adopted) — threat model for the system
- Frozen SAR (optional, when SCK adopted) — security assessment results
- Frozen ORD (informational) — operational readiness evidence

---

## Required Sections

1. Document Control
2. Quality Risk Summary
3. Security Risk Summary
4. Deployment Risk Classification
5. Aggregate Risk Profile
6. Recommended Safeguards
7. Residual Risk Acceptance

---

## Content Rules

### Document Control
**Rules**
- RSA ID must be present (format: RSA-{PROJECT}-{NNN})
- Date must be present
- RER reference must be present with Frozen status confirmed
- RCF reference must be present with Frozen status confirmed
- QGR reference must be present if QAK is adopted (or "QAK not adopted" stated)
- TM and SAR references must be present if SCK is adopted (or "SCK not adopted" stated)
- Spec Version and Principles Version must be present

**Failure Examples**
- Missing RSA ID
- RER or RCF not referenced
- QAK adopted but QGR not referenced

### Quality Risk Summary
**Rules**
- If QAK adopted: summarize QGR findings by severity, list any conditional passes or waivers, note test coverage gaps identified
- If QAK not adopted: state explicitly and note any quality evidence available from ORD
- All claims must cite specific upstream artifact sections
- Do not re-evaluate test results — summarize what QGR found

**Failure Examples**
- QAK adopted but QGR findings not summarized
- Claims without artifact section citations
- Re-interpretation of test results (not allowed — transcribe findings)

### Security Risk Summary
**Rules**
- If SCK adopted: summarize TM threat landscape and SAR findings, note any unmitigated threats, list security conditions or waivers
- If SCK not adopted: state explicitly and note any security evidence available from other sources
- All claims must cite specific upstream artifact sections
- Do not perform security analysis — summarize what SCK found

**Failure Examples**
- SCK adopted but TM/SAR findings not summarized
- Claims without artifact section citations
- Independent security analysis (not allowed — cite upstream)

### Deployment Risk Classification
**Rules**
- Classify the deployment risk as: low, moderate, high, or critical
- Classification must be justified by referencing specific evidence from upstream artifacts
- Factors to consider: change scope, infrastructure changes, dependency changes, data migration, rollback complexity
- Each factor must cite the upstream artifact section that provides the evidence

**Failure Examples**
- Risk classification without justification
- Factors listed without upstream artifact citations
- Classification inconsistent with cited evidence

### Aggregate Risk Profile
**Rules**
- A table summarizing risk across dimensions: Quality, Security, Deployment, Operational
- Each dimension has: risk level (low/moderate/high/critical), key findings summary, source artifact reference
- No dimension may be assessed without citing an upstream artifact
- If no evidence exists for a dimension, state "no evidence available" — do not infer

**Failure Examples**
- Risk dimension without source citation
- Risk level assigned without supporting evidence
- Missing dimension from the profile

### Recommended Safeguards
**Rules**
- Each safeguard must trace to a specific risk finding in §2–§5
- Safeguards must be concrete and actionable (e.g., "reduce initial exposure to 5%" not "be careful")
- Safeguard categories: exposure pacing, monitoring enhancement, rollback trigger tightening, additional testing, communication
- Each safeguard must state: what to do, which risk it mitigates, how it would be implemented in the RP

**Failure Examples**
- Safeguard without traceable risk finding
- Vague safeguard ("monitor closely")
- Safeguard that cannot be implemented in an RP

### Residual Risk Acceptance
**Rules**
- After safeguards are applied, what risks remain?
- Each residual risk must state: the risk, why it cannot be further mitigated, the acceptance rationale
- If no residual risks remain, state "no residual risks identified" with justification
- Residual risks must not contradict recommended safeguards (a safeguard cannot both mitigate and leave a risk residual)

**Failure Examples**
- No residual risk section
- Residual risk that contradicts a safeguard
- Acceptance without rationale

---

## Format Requirements

- RSA ID must follow the standard format
- Risk levels must use standard values: low, moderate, high, critical
- All upstream citations must reference specific artifact sections (not just artifact IDs)
- Tables must use consistent column format

---

## Completeness Rules

- All 7 sections non-empty
- All referenced upstream artifacts confirmed as Frozen
- Every risk finding in §2–§4 traced to a specific upstream artifact section
- Every safeguard in §6 traced to a specific risk finding
- Every residual risk in §7 has acceptance rationale

---

## Relationship Rules

- The RSA is positioned after RER + RCF frozen, before RP generation
- The frozen RSA is an input to the RP prompt — the RP references RSA Recommended Safeguards
- The RSA does not replace QGR, TM, SAR, or any upstream artifact — it aggregates their findings for release context

---

## Hard Gates

| # | Gate | Rule |
|---|------|------|
| 1 | `upstream_evidence_cited` | Every risk claim in §2–§4 cites a specific section of a frozen upstream artifact. No risk claims based on general assertions or the RSA author's independent analysis. |
| 2 | `risk_dimensions_covered` | The Aggregate Risk Profile (§5) covers all four dimensions (Quality, Security, Deployment, Operational) with a risk level and source citation for each. |
| 3 | `safeguard_actionability` | Every recommended safeguard (§6) is concrete, traces to a specific risk finding, and can be implemented in a Release Plan. No vague safeguards. |
| 4 | `residual_risk_justified` | Every residual risk (§7) has an explicit acceptance rationale. No residual risk without justification. No contradiction between safeguards and residual risks. |
| 5 | `no_invented_evidence` | The RSA does not perform its own quality analysis, security analysis, or risk assessment beyond what upstream artifacts provide. All findings are sourced, not originated. |
