# Entry from Engineering Execution Kit (EEK)

**You are here because:** The Operational Readiness Document (ORD) from EEK is frozen.

**What you must bring:**

| Artifact | Status Required | Where to Find It |
|----------|----------------|-----------------|
| Operational Readiness Document (ORD-{PROJECT}-{NNN}) | Frozen | EEK `docs/sdlc/` in the consuming project |
| Work Definition Document (WDD) | Frozen | EEK `docs/sdlc/` — optional but useful for scope verification |
| Release Configuration File (RCF) | Frozen (if one exists) | Check for an org-level RCF before generating a new one |

**First artifact to generate in this kit:** Release Entry Record (RER) — human-authored, no prompt

**Where to start:** `docs/session-setup.md` → "Release Entry Record (RER)"

**What changes at this boundary:**

- You shift from "is it built correctly?" to "is it safe to ship?" The concern moves from implementation quality to deployment risk and exposure management.
- Stakeholders change: the release owner takes accountability. Engineering ownership of the artifact is complete; release operations ownership begins.
- Scope is now about deployment type, traffic exposure, and rollback conditions — not about features or code.

**Common mistakes at this transition:**

| Mistake | How to Avoid |
|---------|--------------|
| Assuming an ORD marked "ready for production" means it is ready to release | The ORD confirms implementation readiness; the REK governs the release process itself. These are separate gates. |
| Generating a new RCF when an org-level one already exists | Check for an existing frozen RCF before creating one. RCF is org-level policy, not per-release. |
| Leaving the release owner as "the team" | RER requires a named individual. A team cannot own a release record. |

**If you arrived here without a complete upstream artifact:**

Stop. Return to EEK, complete the ORD, ensure all 8 hard gates pass (especially `no_open_blockers`), freeze, and then enter REK. REK cannot assess release safety without a fully validated ORD.

---

*For the full entry flow, see `docs/playbook.md`.*
