# Entry from Security & Compliance Kit (SCK)

**You are here because:** SCK (Layer 10) has produced frozen security or compliance artifacts relevant to your release.

**Note:** SCK is cross-cutting — its artifacts arrive at trigger points across multiple kits, not as a sequential entry gate. REK does not wait for SCK to "complete" before starting. SCK artifacts are consumed as inputs to the RSA when available.

**What REK consumes from SCK:**

| Artifact | When Consumed | How Used in REK |
|----------|--------------|-----------------|
| Security Assessment Report (SAR-{PROJECT}-{NNN}) | Frozen, before RSA generation | RSA §Security Risk — provides structured security evidence |
| Dependency Audit Report (DAR-{PROJECT}-{NNN}) | Frozen, before RSA generation | RSA §Security Risk — provides dependency vulnerability evidence |
| Compliance Evidence Record (CER-{PROJECT}-{NNN}) | Frozen, when applicable (P3 compliance initiatives) | RSA §Compliance Risk — provides regulatory and compliance evidence |

**What REK does NOT consume directly from SCK:**

| Artifact | Consumed By | Why Not REK |
|----------|-------------|-------------|
| Threat Model (TM) | QAK (informs Verification Plan) | TM is an upstream input to QAK, not a direct REK input. REK sees its effects through the QGR and SAR. |

**SCK artifacts are optional inputs:**

If your organization has not adopted SCK, or if SCK artifacts are not yet available, the RSA §Security Risk section uses whatever security evidence is available (e.g., ORD security notes, team assessment). SCK adoption strengthens the RSA but is not a gate to entering or progressing through REK.

**What changes when SCK artifacts are present:**

- The RSA has structured, validated security and dependency evidence instead of informal assessment. This increases confidence in the release safety determination.
- For compliance initiatives (P3 preset), the CER provides formal evidence that regulatory requirements are satisfied — the RSA §Compliance Risk section references it directly.

**Common mistakes when consuming SCK artifacts:**

| Mistake | How to Avoid |
|---------|--------------|
| Waiting for all SCK artifacts before starting REK | SCK is cross-cutting, not a sequential gate. Start REK when the ORD (and QGR, if applicable) is frozen. Incorporate SCK artifacts into the RSA as they become available. |
| Treating SCK as a blocking gate to REK entry | SCK artifacts are inputs to the RSA, not entry prerequisites. REK entry requires a frozen ORD (and frozen QGR if QAK is adopted). |
| Ignoring SAR or DAR findings in the RSA | If frozen SAR or DAR artifacts exist, the RSA must reference their findings. Do not generate an RSA that omits available security evidence. |
| Using TM directly in REK instead of through SAR | The TM feeds QAK and SCK analysis. REK sees security posture through the SAR, not the raw threat model. |

---

*For the full entry flow, see `docs/playbook.md`.*
