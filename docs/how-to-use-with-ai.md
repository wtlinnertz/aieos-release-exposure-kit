# How to Use This Kit with AI

This document explains how to use AI tools effectively with the Release & Exposure Kit. Following these practices prevents the most common failure mode: AI that generates plausible-looking release artifacts that fail validation or contain silently wrong information.

---

## The Core Rule: Generate and Validate in Separate Sessions

Never run the generation prompt and validator for the same artifact in the same AI conversation session.

**Why:** An AI session that generated content will defend that content. It will interpret ambiguous validator criteria in ways that favor the artifact it produced. This is not a bug — it is how language models work. Separation prevents self-validation bias.

**How:**
1. Session A: Run the generation prompt → get a draft artifact
2. End or pause Session A
3. Session B: Run the validator prompt with the artifact as input → get a structured JSON verdict
4. Review the JSON verdict yourself
5. Return to Session A (or open Session C): provide the blocking issues and regenerate

This separation is a structural requirement, not a suggestion.

---

## Session Setup

### Generation Sessions

Before running a generation prompt, load:
1. The generation prompt file (e.g., `rp-prompt.md`)
2. The spec file the prompt references (e.g., `rp-spec.md`)
3. All required upstream artifacts in **frozen** status

Start the session with a statement like:

> I need to generate a Release Plan. I'm providing the generation prompt, the spec it references, and the frozen upstream artifacts. Please generate the artifact exactly as the prompt instructs without adding analysis or caveats outside the template structure.

### Validation Sessions

Before running a validator, load:
1. The validator file (e.g., `rp-validator.md`)
2. The spec file the validator references (e.g., `rp-spec.md`)
3. The artifact being validated (draft or revised)

Start the session with:

> I need to validate this artifact. Evaluate it strictly against the spec and produce the JSON output exactly as specified. Do not suggest how to fix issues — only identify what is missing or insufficient.

---

## Artifact-Specific Session Patterns

### Release Entry Record

The RER is human-authored. AI is **not recommended** for RER generation — it requires information only the release owner knows (the authorization decision reference, the named owner, the ORD ID). Complete the template manually, then validate:

```
Session: Validate RER
Input: release-entry-validator.md + rr-spec content + completed RER
```

### Release Context File (RCF)

1. Complete the intake form manually (this is the most important step — the intake captures your org's actual standards)
2. Human review of the intake form before AI generation
3. Generation session: `rcf-prompt.md` + `rcf-spec.md` + completed intake form + principles files
4. Validation session: `rcf-validator.md` + `rcf-spec.md` + draft RCF
5. Fix blocking issues in the generation session
6. Human freeze (RCF must be approved by someone with release policy authority)

### Release Plan (RP)

1. Generation session: `rp-prompt.md` + `rp-spec.md` + frozen ORD + frozen RCF + frozen RER
2. Human review of the generated RP — specifically check rollback conditions (observable signals, not qualitative) and metric baselines (sourced from ORD, not invented)
3. Optional: Rollout risk assessment session: `rollout-risk-assessment-prompt.md` + frozen RP (separate session)
4. Validation session: `rp-validator.md` + `rp-spec.md` + draft RP
5. Fix blocking issues
6. Human freeze + Pre-Release Authorization Checklist completed immediately before execution

### Release Record (RR)

The RR requires evidence collected during execution. AI structures the evidence — it does not create it.

1. Collect all execution evidence before starting the RR session: deployment logs, metric values with timestamps, alert records, decision records (who decided, when, why)
2. Generation session: `rr-prompt.md` + `rr-spec.md` + frozen RP + collected evidence
3. Review carefully: any "[EVIDENCE PENDING]" or "[REQUIRES CONCRETE EVIDENCE]" markers mean evidence is missing — do not freeze the RR until all evidence fields are populated
4. Validation session: `rr-validator.md` + `rr-spec.md` + draft RR
5. Fix blocking issues
6. Human freeze

---

## What to Do When the Validator Finds Blocking Issues

1. **Read the blocking issue description carefully.** It identifies what is missing or insufficient, referenced to a section or field in the artifact.

2. **Do not ask the generation session to "fix" the validator output.** Instead, provide the blocking issues to the generation session as specific inputs:

   > The validator found the following blocking issues. Please address each one and regenerate the affected sections only. [paste blocking issues JSON]

3. **Do not modify the validator to pass an artifact that should fail.** If the validator is identifying a genuine gap, fix the gap.

4. **If a blocking issue appears incorrect,** check the spec directly. If the validator is wrong (a spec rule is not clearly reflected in the validator), this is a kit maintenance issue — note it, but still fix the actual artifact content.

---

## When Not to Use AI

**Use a human directly for:**
- The Release Entry Record (the RER requires information only the release owner has)
- The Pre-Release Authorization Checklist (human sign-off required, not AI-generated)
- Rollback decisions during execution (observable signal evaluation is human judgment)
- Release disposition declarations (the outcome of a release is a human decision)

**AI helps with:**
- Drafting the RCF from an intake form the human has completed
- Structuring the RP from frozen upstream artifacts
- Identifying rollout risks before execution (rollout risk assessment)
- Structuring execution evidence into the RR format
- Drafting release communications for human review

---

## Prompting Tips

**Be explicit about frozen status.** When providing upstream artifacts, state their status:

> I'm providing the frozen ORD (status: Frozen, version: ORD-TASKFLOW-001). Do not modify its content — treat it as a read-only input.

**Flag missing inputs rather than inferring.** If an upstream artifact is incomplete, ask the AI to mark missing information explicitly:

> If any required input is absent or unclear, mark it with [MISSING: description] rather than inferring a value.

**One artifact per session.** Do not generate multiple artifacts in the same session. Context from one artifact bleeds into the next. Keep each artifact in its own generation session.

**Do not ask for analysis in a generation session.** Generation sessions produce artifacts. If you want analysis (risk assessment, retrospective), use a utility prompt in a separate session.
