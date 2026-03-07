# Release Entry Record — Specification

The Release Entry Record is a lightweight gate that must be completed before beginning any release planning in the Release & Exposure Kit. It confirms the system is operationally ready for release, the release is appropriately authorized, and a named release owner has accepted accountability.

This is a **boundary contract**, not a governed artifact. The record is human-authored. It is validated against this spec before release planning begins.

---

## What This Artifact Is Not

- **Not a Release Plan.** The RER is an entry gate that confirms prerequisites for release planning — it does not define the release strategy or execution approach. Those belong in the RP.
- **Not an authorization to release.** The RER authorizes beginning release planning. Actual release execution requires a validated and frozen RP plus a completed Pre-Release Authorization Checklist.
- **Not a substitute for the ORD.** The RER confirms the ORD is frozen; it does not summarize or replace its content.

---

## Purpose

The release entry spec serves two roles:

1. **Intake gate** — Confirms that the upstream ORD is frozen, the release scope is declared, a release owner is named, and an authorization decision is on record; prevents releases from beginning without verified operational readiness
2. **Path record** — Freezes the release entry decision so it is traceable and auditable

---

## Upstream Dependencies

- Frozen Operational Readiness Document (ORD) from the Engineering Execution Kit — must be in Frozen status; all 8 hard gates must have passed

---

## Required Sections

1. Document Control
2. Upstream Verification
3. Release Scope
4. Release Owner
5. Priority Confirmation
6. Completeness Checklist
7. Freeze Declaration

---

## Content Rules

### Document Control
**Rules**
- Record ID must be present (format: RER-{PROJECT}-{NNN})
- Date must be present
- A brief release summary must be present (1-2 sentences describing what is being released)

**Failure Examples**
- Missing Record ID
- Release summary absent or "TBD"

### Upstream Verification
**Rules**
- The ORD being released against must be identified by ID
- The ORD status must be confirmed as Frozen (not Draft or Approved)
- No release may begin against an ORD that is not in Frozen status

**Failure Examples**
- ORD ID missing or "unknown"
- ORD status listed as "Approved" rather than "Frozen"
- ORD field left blank

### Release Scope
**Rules**
- Must declare what is being released (system, service, feature, or component name)
- Must state the release type: canary | blue-green | rolling | direct-full
- Must state the initial exposure target (e.g., "canary: 5% of production traffic" or "direct: 100% of users")

**Failure Examples**
- "Everything in the ORD" — not a scope statement
- Release type absent or "TBD"
- Initial exposure target absent

### Release Owner
**Rules**
- A named individual must be identified as release owner
- The release owner's authorization level must be stated (e.g., "senior engineer — authorized up to 50% exposure without escalation")
- Authorization level must be consistent with what the Release Context File defines (if an RCF exists); if no RCF exists, the authorization level stated here governs this release

**Failure Examples**
- "The team" — not a named individual
- Authorization level absent
- Release owner named but authorization level not stated

### Priority Confirmation
**Rules**
- Must confirm a release authorization decision exists on record
- Must provide a traceable reference — a date and approver name, a tracking system ID, or a reference to a planning session record
- "We decided to release it" without a reference is not sufficient
- Sprint completion, PR merge, or ORD approval do not constitute release authorization — a separate explicit decision is required

**Failure Examples**
- "Yes, authorized" with no traceable reference
- "ORD was approved on [date]" — ORD approval is not release authorization
- Field blank or absent

---

## Format Requirements

- Release type must be selected from the enumerated list (canary / blue-green / rolling / direct-full)
- ORD ID must reference a specific document ID, not a URL shortener or informal nickname
- Release owner must be a person's name, not a team name or role title

---

## Completeness Rules

- All five substantive sections must be present and non-empty
- ORD must be in Frozen status
- Release type must be one of the four enumerated types
- Named release owner with authorization level
- Authorization reference must be traceable

---

## Relationship Rules

- The Release Entry Record gates release planning — no RCF generation or RP generation may begin until the record is frozen and validated
- The Release Entry Record does not replace the RCF or RP — those follow after the entry gate passes
- If an existing RCF is being reused, the RER must still be completed and frozen for each new release engagement

---

## Hard Gates

1. **document_control** — Record ID, date, and release summary present
2. **upstream_reference** — ORD ID referenced; ORD status confirmed as Frozen
3. **release_scope** — What is being released declared; release type selected from enumerated list; initial exposure target stated
4. **release_owner** — Named individual as release owner with authorization level stated
5. **priority_confirmed** — Release authorization confirmed with a traceable reference; not inferred from ORD approval or sprint events
