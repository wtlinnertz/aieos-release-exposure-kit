# Release Context Intake Form

Use this form to capture the organizational release policy inputs needed to generate a Release Context File (RCF). This form is **human-authored** — fill it in based on your organization's actual standards and practices. Do not leave sections blank; if a topic is not yet defined, note that explicitly.

After completing this form, review it for accuracy before using it as input to `rcf-prompt.md`.

---

## Organization and Scope

- Organization / Team Name:
- RCF Applicability Scope: (which systems, products, or teams this RCF will govern — be specific)
- What is explicitly excluded from this RCF's scope:
- Owner (team or role name, not individual):

---

## System Risk Tiers

Define 2-4 risk tiers for systems covered by this RCF. Tier 1 is highest risk.

| Tier | Name | Criteria (what makes a system this tier?) |
|------|------|------------------------------------------|
| 1 | | |
| 2 | | |
| 3 (optional) | | |

---

## Deployment Standards

**Permitted deployment strategies by tier:**

| Tier | Permitted Strategies | Prohibited Strategies |
|------|---------------------|----------------------|
| Tier 1 | | |
| Tier 2 | | |
| Tier 3 (if defined) | | |

**Required verification steps after deployment (per strategy):**

- Canary deployment verification:
- Blue-green deployment verification:
- Rolling deployment verification:
- Direct-full deployment verification:
- (Add only strategies your organization uses)

---

## Exposure Standards

- Is progressive exposure mandatory for Tier 1 changes? Yes / No
- Is progressive exposure mandatory for Tier 2 changes? Yes / No
- Maximum exposure increment per stage (percentage points): ______
- Minimum observation period between exposure stages: ______ (hours/days)
- Is direct-full exposure ever permitted for Tier 1 systems? Yes / No
  - If yes, under what conditions:
- Feature flag maximum simultaneous active flags per system: ______
- Feature flag cleanup date requirement: ______ (days after reaching full exposure)

---

## Release Authorization Model

**Authorization levels:**

| Level | Role / Title | Maximum Exposure Without Escalation |
|-------|-------------|--------------------------------------|
| 1 | | |
| 2 | | |
| 3 (if applicable) | | |

**Escalation triggers** (what requires escalating to higher authorization level):

**Emergency release procedure** (what steps are required for releases bypassing normal planning):

**Cross-boundary releases** (releases affecting multiple teams or systems — procedure):

---

## Communication Requirements

**Internal stakeholder notifications:**

| Audience | Notification Stage | Format / Channel |
|----------|-------------------|-----------------|
| | | |

**Customer-facing communication triggers** (what kinds of changes require customer notification):

**Partner / API consumer notification requirements:**
(or "Not applicable" with justification)

---

## Monitoring Requirements

**Minimum watch period per exposure stage:** ______ (hours)

**Required metric categories** (list at least 3 that must be monitored for every release):
1.
2.
3.

**Alert threshold policy:**
- [ ] Thresholds must be relative to established baseline
- [ ] Absolute thresholds acceptable if well-established
- [ ] Either is acceptable
- Default approach when no baseline exists:

**SLO baseline documentation requirement:**
(When and how must the pre-release SLO baseline be captured and stored?)

---

## Existing Exceptions or Special Cases

List any known exceptions to the above standards that should be documented in the RCF:

| Exception | Affected Standard | Justification | Expiry Condition |
|-----------|------------------|---------------|-----------------|
| | | | |

---

## Reviewer Notes

Human review notes before using this form as AI generation input:
_(Confirm accuracy of all entries, especially authorization levels and tier criteria)_
