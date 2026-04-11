# How to Adapt This Kit to Your Organization

This document explains how to adopt the Release & Exposure Kit in your organization, including how to configure the Release Context File (RCF) for your release standards, how to adapt the system risk tier model, and how to scope the kit to your environment.

---

## What You Configure vs. What You Keep

**You configure:**
- System risk tier criteria and names (RCF §2) — what makes a system high-risk vs. standard-risk in your context
- Permitted deployment strategies by tier (RCF §2)
- Exposure increment limits and observation periods (RCF §3)
- Authorization levels and thresholds (RCF §4)
- Communication requirements and audience definitions (RCF §5)
- Monitoring metric categories and alert threshold policy (RCF §6)
- Feature flag governance rules (RCF §3)

**You keep (process architecture — not configurable):**
- The four-artifact structure: RER → RCF → RP → RR
- The entry gate pattern: Release Entry Record gates all release planning
- The freeze-before-promote rule: upstream artifacts must be frozen before downstream generation
- The evidence standard: concrete, timestamped, traceable, retrievable
- The separation of generation and validation sessions
- The RP Pre-Release Authorization Checklist as a go/no-go gate

The structural rules are in `docs/governance-model.md`. They exist because the kit's value depends on them.

---

## Configuring the Release Context File

The RCF is your primary configuration point. Use the intake form to capture your organization's standards before generating the RCF.

### System Risk Tier Criteria

Define tiers based on your production impact model. Examples:

**Revenue-critical SaaS:**
- Tier 1: Services that directly handle payments, authentication, or user data storage
- Tier 2: User-facing features and APIs with external consumers
- Tier 3: Internal tooling and background services

**Platform / infrastructure:**
- Tier 1: Shared infrastructure serving multiple products (databases, message queues, API gateways)
- Tier 2: Single-product backend services
- Tier 3: Developer tooling and internal services

The criteria must be evaluable — a team with a new system should be able to determine its tier without asking for help.

### Exposure Increment and Observation Periods

These two parameters are the most consequential in your RCF:

| Setting | Conservative (high-risk org) | Moderate | Minimal (low-risk or internal) |
|---------|------------------------------|----------|-------------------------------|
| Max increment per stage | 10% | 25% | 50% |
| Min observation period | 24 hours | 4-8 hours | 1-2 hours |

Start conservative. You can loosen these later; loosening them after an incident is harder.

The observation period should reflect your traffic patterns. If your system has 10x more traffic on weekdays than weekends, a 2-hour observation period that starts at midnight captures almost no signal.

### Authorization Levels

Define at most 3-4 levels. More levels add bureaucracy without adding safety. Common patterns:

**Two-level (small team):**
- Engineer: up to 25% exposure
- Engineering lead: up to 100%

**Three-level (medium team):**
- Engineer: up to 10%
- Senior engineer / lead: up to 50%
- Engineering manager: up to 100%

Escalation triggers should include: any rollback event, Tier 1 system exposure above a threshold, customer-impacting incidents.

### Feature Flag Governance

Key questions for your RCF:
- How long after reaching full exposure must flags be removed? (Common: 14-30 days)
- How many simultaneous active flags per system? (Common: 3-5)
- What is the flag naming convention?

Flags that are never removed become permanent configuration — the cleanup date requirement prevents this.

---

## Scoping the Kit to Your Environment

### Single Team Adoption

Start with one team and one RCF. The team's release lead authors the intake form; the RCF covers all systems owned by the team.

The RCF is shared across all releases the team conducts. Revise it only when the team's release policy genuinely changes.

### Multi-Team Adoption

Each team (or product area) authors its own RCF scoped to its systems. Cross-team releases require coordination at the RER stage — the release owner should be from the team with the highest-risk change, and the RCF used should be the stricter of the two teams' policies.

### Enterprise / Platform

For large organizations with a central release policy team: the central team authors and owns the RCF. Individual product teams generate Release Plans (RPs) from the shared RCF. The RCF is a stable, infrequently-changed artifact; product teams do not modify it.

---

## What to Do on First Use

1. **Fill out the intake form first** (`docs/artifacts/release-context-intake-template.md`). Don't start writing an RCF directly — the intake form organizes your thinking.

2. **Review the intake form with your release lead or engineering manager** before generating the RCF. The authorization levels and tier criteria in particular must be explicitly agreed-upon.

3. **Generate, validate, and freeze the RCF before running your first release.** The RCF must be frozen before any RP generation begins.

4. **Run the first release as a learning exercise.** Use `rollout-risk-assessment-prompt.md` on your first Release Plan to identify gaps in your planning before execution.

5. **After your first release, review the RR** against what you expected. If your observation periods were too short, your thresholds too loose, or your monitoring missed signal — update the RCF before the next release.

---

## When the RCF Needs to Change

**Normal RCF revision:**
1. The release policy team identifies the change needed
2. Create a new RCF version (increment version number)
3. Update the sections that changed
4. Re-validate and re-freeze with appropriate approver
5. Communicate the new version to teams generating Release Plans

**What does not require a new RCF:**
- A single release that needs an exception to RCF standards: document the exception in the RP, not in the RCF
- A new system being added to scope: the existing RCF already covers it if it's within the stated applicability scope

**What requires a new RCF version:**
- Authorization thresholds change
- New deployment strategies added or removed
- Observation period or increment limits change
- Applicability scope changes

---

## Common Adaptation Mistakes

**Mistake: Putting all standards in the RP**
The RP is per-release. Standards (thresholds, tiers, requirements) belong in the RCF and are reused across releases. If you find yourself repeating the same authorization model or threshold definitions in every RP, move them to the RCF.

**Mistake: Making the RCF too strict for the team's actual practices**
A standard you can't or won't enforce is worse than no standard — it teaches the team to treat the kit as checkbox theater. Calibrate the RCF to practices you will actually enforce, then tighten over time.

**Mistake: Treating the validator as an obstacle**
The validator enforces the RCF's own standards against the RP. If an RP fails the validator, it means the plan doesn't comply with the organization's stated release policy. Fix the plan, not the validator.

**Mistake: Skipping the RER for "small" releases**
Every release needs a Release Entry Record. The RER exists to prevent the assumption that "this is small enough to skip the process" — which is precisely when processes get skipped and incidents happen. The RER takes minutes to complete.
