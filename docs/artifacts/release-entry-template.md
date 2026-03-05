# Release Entry Record

A lightweight gate that must be completed and frozen before beginning release planning in the Release & Exposure Kit. Confirms the upstream ORD is frozen, declares release scope and type, names the release owner, and confirms authorization exists on record.

This record is **human-authored**. It is validated against `release-entry-spec.md` before RCF or RP generation begins.

---

## Document Control

- Record ID: (format: RER-{PROJECT}-{NNN})
- Date:
- Release Summary: (1-2 sentences describing what is being released)

---

## Upstream Verification

- ORD ID:
- ORD Status: (must be: Frozen)
- ORD Validation Reference: (where the PASS validation output is stored)

---

## Release Scope

- What is being released: (system name, service, feature, or component)
- Release Type: (select one: canary | blue-green | rolling | direct-full)
- Initial Exposure Target: (e.g., "canary: 5% of production traffic" or "direct: 100% of users")

---

## Release Owner

- Named Release Owner:
- Authorization Level: (e.g., "senior engineer — authorized up to 50% exposure without escalation per RCF-ACME-001 §4")

---

## Priority Confirmation

- Release authorized: Yes / No
- Authorization Reference: (date + approver name, planning session record, or tracking system ID)

_Note: ORD approval and sprint completion do not constitute release authorization. A separate, explicit release decision is required._

---

## Completeness Checklist

Before validating and freezing this record, confirm:

- [ ] Record ID and date are present
- [ ] Release summary is present (not blank or "TBD")
- [ ] ORD ID is present and status confirmed as Frozen
- [ ] Release type is selected from the enumerated list (canary / blue-green / rolling / direct-full)
- [ ] Initial exposure target is stated
- [ ] Named release owner (individual, not team) is present
- [ ] Authorization level is stated
- [ ] Priority confirmation has a traceable reference (not just "yes")

---

## Freeze Declaration

This Release Entry Record is validated and frozen. Release planning may proceed.

- Validated Against: `release-entry-spec.md`
- Validation Result: PASS
- Frozen By:
- Date:
