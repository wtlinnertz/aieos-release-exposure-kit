# Entry from Quality Assurance Kit (QAK)

**You are here because:** The Quality Gate Report (QGR) from QAK is frozen with a PASS or CONDITIONAL disposition.

**Note:** QAK (Layer 9) is optional. If your organization has not adopted QAK, REK entry is from EEK directly — see `docs/entry-from-eek.md`. This boundary applies only when QAK is in the initiative flow.

**What you must bring:**

| Artifact | Status Required | Where to Find It |
|----------|----------------|-----------------|
| Quality Gate Report (QGR-{PROJECT}-{NNN}) | Frozen, disposition PASS or CONDITIONAL | QAK `docs/sdlc/` in the consuming project |
| Operational Readiness Document (ORD-{PROJECT}-{NNN}) | Frozen | EEK `docs/sdlc/` — still required; QAK does not replace the ORD |
| Release Configuration File (RCF) | Frozen (if one exists) | Check for an org-level RCF before generating a new one |

**First artifact to generate in this kit:** Release Entry Record (RER) — human-authored, no prompt

**Where to start:** `docs/session-setup.md` → "Release Entry Record (RER)"

**What changes at this boundary:**

- You shift from "does it meet quality standards?" to "is it safe to ship?" The concern moves from test coverage, defect density, and quality criteria to deployment risk and exposure management.
- The QGR provides structured quality evidence that feeds directly into the RSA §Quality Risk section, replacing ad-hoc quality assessment.
- If the QGR disposition is CONDITIONAL, the accepted conditions carry forward — the RSA must reference them and the Release Plan must account for any residual risk they introduce.

**QGR disposition routing:**

| QGR Disposition | Action |
|----------------|--------|
| PASS | Proceed to REK. QGR feeds RSA as quality evidence. |
| CONDITIONAL | Proceed to REK. RSA must explicitly reference accepted conditions from the QGR. RP must account for residual risk. |
| FAIL | Do NOT enter REK. Return to EEK to address blocking quality issues, then re-run QAK. |

**Common mistakes at this transition:**

| Mistake | How to Avoid |
|---------|--------------|
| Entering REK with a QGR disposition of FAIL | A FAIL disposition means quality gates were not met. Return to EEK, resolve the issues, and re-validate through QAK before entering REK. |
| Ignoring CONDITIONAL conditions in the RSA | CONDITIONAL means "proceed with known risks." The RSA §Quality Risk must explicitly list each accepted condition and its mitigation. Do not treat CONDITIONAL as equivalent to PASS. |
| Treating QAK as a replacement for the ORD | QAK validates quality criteria; the ORD confirms operational readiness. Both are required inputs to REK when QAK is adopted. |

**If you arrived here without a complete upstream artifact:**

Stop. If the QGR is not frozen or has a FAIL disposition, return to the appropriate upstream kit. If the QGR is frozen CONDITIONAL, verify you have documented the accepted conditions before proceeding. REK cannot assess release safety without validated quality evidence.

---

*For the full entry flow, see `docs/playbook.md`.*
