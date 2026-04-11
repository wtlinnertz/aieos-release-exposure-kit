# Release Safety Assessment

## §1 Document Control

| Field | Value |
|-------|-------|
| RSA ID | RSA-{PROJECT}-{NNN} |
| Date | {YYYY-MM-DD} |
| Status | {Draft / Validated / Frozen} |
| Release Scope | {what is being released — consistent with RER} |
| RER Reference | {RER ID, confirmed Frozen} |
| RCF Reference | {RCF ID, confirmed Frozen} |
| QGR Reference | {QGR ID, confirmed Frozen — or "QAK not adopted"} |
| TM Reference | {TM ID, confirmed Frozen — or "SCK not adopted"} |
| SAR Reference | {SAR ID, confirmed Frozen — or "SCK not adopted"} |
| ORD Reference | {ORD ID, informational} |
| Governance Model Version | {version from governance-model.md} |
| Prompt Version | {version from rsa-prompt.md} |
| Spec Version | {version from rsa-spec.md} |
| Principles Version | {version from principles file, or N/A} |

---

## §2 Quality Risk Summary

### QAK Evidence

{If QAK adopted: summary of QGR findings. If not adopted: "QAK not adopted for this initiative."}

| Aspect | Finding | Source | Severity |
|--------|---------|--------|----------|
| {test coverage / gate results / waivers / etc.} | {what was found} | {QGR §X or ORD §X} | {low / moderate / high / critical} |

### Quality Risk Assessment

{1–2 sentence overall quality risk statement with citations.}

---

## §3 Security Risk Summary

### SCK Evidence

{If SCK adopted: summary of TM and SAR findings. If not adopted: "SCK not adopted for this initiative."}

| Aspect | Finding | Source | Severity |
|--------|---------|--------|----------|
| {threat landscape / vulnerabilities / mitigations / waivers / etc.} | {what was found} | {TM §X or SAR §X} | {low / moderate / high / critical} |

### Security Risk Assessment

{1–2 sentence overall security risk statement with citations.}

---

## §4 Deployment Risk Classification

**Risk Level:** {low / moderate / high / critical}

| Factor | Evidence | Source | Risk Contribution |
|--------|----------|--------|-------------------|
| {change scope / infrastructure changes / dependency changes / data migration / rollback complexity} | {what the evidence shows} | {ORD §X or ISPEC §X or RER §X} | {low / moderate / high / critical} |

**Justification:** {1–2 sentences explaining the overall deployment risk classification based on the factors above.}

---

## §5 Aggregate Risk Profile

| Dimension | Risk Level | Key Findings | Source |
|-----------|-----------|-------------|--------|
| Quality | {low / moderate / high / critical} | {1-sentence summary} | {QGR §X or ORD §X} |
| Security | {low / moderate / high / critical} | {1-sentence summary} | {TM §X or SAR §X or "no evidence"} |
| Deployment | {low / moderate / high / critical} | {1-sentence summary} | {ORD §X, RER §X} |
| Operational | {low / moderate / high / critical} | {1-sentence summary} | {ORD §X, ISPEC §X} |

---

## §6 Recommended Safeguards

| # | Safeguard | Risk Mitigated | Source Finding | RP Implementation |
|---|-----------|---------------|----------------|-------------------|
| 1 | {concrete, actionable safeguard} | {which risk from §2–§5} | {§X finding reference} | {how this translates to an RP section} |

---

## §7 Residual Risk Acceptance

| # | Residual Risk | Why Not Further Mitigated | Acceptance Rationale |
|---|--------------|--------------------------|---------------------|
| 1 | {risk that remains after safeguards} | {why safeguards cannot eliminate it} | {why proceeding is acceptable} |

{Or: "No residual risks identified. All identified risks are addressed by recommended safeguards."}

---

## Freeze Declaration

| Field | Value |
|-------|-------|
| Frozen By | {name and role} |
| Freeze Date | {YYYY-MM-DD} |
