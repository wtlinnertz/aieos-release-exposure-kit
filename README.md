# aieos-release-exposure-kit

**Layer 5 of the AIEOS system — Release & Exposure**

This kit governs how production-ready systems are shipped safely. It receives a frozen Operational Readiness Document (ORD) from the Engineering Execution Kit and produces a Release Record that documents what was released, how, and in what state — ready for handoff to the Reliability & Resilience Kit (Layer 6).

---

## What This Kit Does

The Engineering Execution Kit (Layer 4) produces an ORD that declares a system is operationally ready for production. But "operationally ready" is not the same as "in production." This kit governs the gap:

- **Deployment strategy** — How is the system deployed? (canary, blue-green, rolling, direct)
- **Progressive exposure** — How are users introduced to the change? (feature flags, percentage rollouts, segment targeting)
- **Rollback conditions** — What observable signals trigger a rollback, and who decides?
- **Release authorization** — Who authorizes release at each exposure stage?
- **Post-release monitoring** — What do we watch, for how long, and what signals matter?

---

## Artifact Types

This kit produces three governed artifact types in sequence:

| Step | Artifact | Purpose |
|------|----------|---------|
| 0 | Release Entry Record (RER) | Entry gate: confirms ORD is frozen, release is authorized |
| 1 | Release Context File (RCF) | Org-level release policy (reusable across releases) |
| 2 | Release Plan (RP) | Initiative-specific release plan |
| 3 | Release Record (RR) | Post-release evidence artifact (what happened) |

Each governed artifact type has exactly four governing files: spec, template, prompt, validator.

---

## Quick Start

1. Read `docs/playbook.md` — the complete process definition
2. Read `docs/how-to-use-with-ai.md` — session setup and AI tool guidance
3. See `examples/basic-release/` — a worked example (TaskFlow Notifications release)

---

## Repository Structure

```
docs/
  principles/          # Organizational release policy (input material)
  specs/               # Content rules and hard gates per artifact type
  artifacts/           # Templates and intake forms
  prompts/             # AI generation + utility prompts
  validators/          # Quality gate definitions
  playbook.md          # End-to-end process definition
  index.md             # Documentation entry point
  how-to-adapt.md      # Organizational adoption guidance
  how-to-use-with-ai.md # AI tool usage guide
  governance-model.md  # AIEOS structural rules (reference)
examples/
  basic-release/       # Worked example: TaskFlow Notifications release
tests/
  kit-test-plan.md     # Structural integrity checks and flow scenarios
CLAUDE.md              # AI operating instructions
```

---

## AIEOS Layer

| Layer | Kit | Status |
|-------|-----|--------|
| 2. Product Intelligence | `aieos-product-intelligence-kit` | Built |
| 4. Engineering Execution | `aieos-engineering-execution-kit` | Built |
| **5. Release & Exposure** | **`aieos-release-exposure-kit`** | **Built** |
| 6. Reliability & Resilience | `aieos-reliability-resilience-kit` | Planned |

See `aieos-governance-foundation/docs/layer-model.md` for the full seven-layer model.
