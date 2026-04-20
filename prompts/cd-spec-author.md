# CD Spec Authoring Prompt

Walks a developer through tailoring
`templates/cd-spec/python-k8s-flux.yaml` to their service. CD spec
authoring is heavier than CI because the environment graph is
service-specific; this prompt spends most of its time on that graph.

Interview-style. One question at a time. Record answers into the draft.
Never present the raw YAML and ask "any changes?" — the value is surfacing
decisions the developer hasn't thought about.

## When this runs

- Trigger: after release authorization in Layer 5.
- Input artifacts: frozen CI spec (`.aieos/ci.spec.yaml`), recent CI run
  record (for the actual artifact digest), release authorization record.
- Output: a draft CD spec committed to the target repo at
  `.aieos/cd.spec.yaml`. Validated against
  `aieos-governance-foundation/schema/cd-spec.schema.json` before freeze.

## Invariants

- Every action must exist in `taxonomy/actions-v1.md` (frozen at
  `v1.0-taxonomy`).
- Environment DAG: promotions form an acyclic graph with typed edges
  (`promote`, `auto-promote`, `manual-gate-required`).
- `artifact_ref` must reference the artifact the CI pipeline produced —
  a full OCI image digest, not a tag.
- Reserved v1.1 fields (`bake_duration`, `verification_interval`,
  `rollback_on_degradation`, `exposure_policy`, `lifetime: pr-scope`) are
  present in the schema but not evaluated by v1; leave them `null` or
  omit.

## Interview

### Artifact digest

"What is the exact OCI digest of the artifact you're deploying? This comes
from the most recent successful CI run's `publish.artifact` step — a full
`registry/org/app@sha256:...` reference. If you have a tag but not a
digest, fetch the digest first; the CD spec refuses tag-only references."

Record: `artifact_ref`.

### Environment inventory

"List every environment this artifact flows through. The template assumes
`dev -> staging -> prod`. Common variations:

- Add a `canary` after `staging` and before `prod` for progressive
  exposure.
- Add a `sandbox` before `dev` for feature-branch testing.
- Skip `staging` if your org's deploy policy permits dev-to-prod directly.

Which environments apply to your service?"

Record: environments array.

### Promotion edges

"For each promotion between environments, decide the gate type:

- `auto-promote` — promotes when the source environment's verify.*
  actions pass. Typical for `dev -> staging` in low-risk services.
- `promote` — manual trigger but no human approval required.
- `manual-gate-required` — human approval required before the runner
  executes the promotion. Typical for `staging -> prod`.

What's the type for each edge? Default template: auto-promote dev->staging,
manual-gate-required staging->prod."

Record: promotions array with `from`, `to`, `type`.

### Per-environment verification

"For each environment, what verify actions run post-deploy?

- `verify.smoke` — minimal liveness check (GET / expecting 200). Default
  for every environment.
- `verify.health` — deeper health checks against /healthz or equivalent.
  Default for prod; add to staging if your release gate depends on it.
- `verify.slo` — continuous SLO measurement over a window. Prod-only in
  v1; v1.1 lets you add it to bake-time promotions.

Any environment where the defaults aren't right?"

Record: per-environment `actions` array.

### verify.smoke check configuration

"`verify.smoke` defaults to `expected_status: 200`. If your service's
liveness endpoint returns a different status, override. Also decide:

- Path — defaults to `/`. Common alternatives: `/ping`, `/livez`.
- Method — GET by default."

Record: `criteria.method`, `criteria.path`, `criteria.expected_status`.

### verify.health configuration (prod)

"List the endpoints verify.health probes. Typical: `/healthz`, `/readyz`,
`/metrics`. For each, the expected status code (usually 200).

Any dependency-probe endpoints (e.g., `/healthz/deep` that pings the
database)? Those should be in the health set."

Record: `actions[verify.health].criteria.paths`, `expected_status_codes`.

### verify.slo configuration (prod)

"`verify.slo` defaults to `min_success_rate: 0.99` over a `window_seconds:
600` window. Questions:

- Is 99% right for your service? P95 latency or throughput SLOs?
- What's the window? 10 minutes is tight — 1 hour is more forgiving.
- What's the metrics source? (Prometheus is the v1 assumption — v1.1
  supports others.)"

Record: SLO definitions array + measurement_window_seconds.

### Reconcile readiness

"`deploy.environment` defaults to `reconciled_within_seconds: 300` (5
minutes). For large manifests or slow-to-schedule workloads, bump to 600
or 900. For small services, 180 is reasonable."

Record: per-environment `deploy.environment.criteria.reconciled_within_seconds`.

### Rollback conditions

"The template triggers `deploy.rollback` when any `verify.*` action FAILs.
Is that right, or do you want finer-grained rules (e.g., rollback only on
verify.slo, not on verify.smoke — smoke failures might mean a flaky
health check rather than a bad deploy)?"

Record: `rollback_conditions.trigger_on`, `rollback_to` strategy.

### Timeouts and retries

"Default `timeout_seconds: 3600` (1 hour). `retry.max_retries: 0` — CD
retries are dangerous (double-deploy). Override only if your reconciler
needs headroom."

Record: `policies.timeout_seconds`, `policies.retry`.

### Adapter preferences

"`deploy.environment` defaults to `adapter-flux-handoff`. The verify.*
adapters named in the template (`adapter-http-smoke`, `adapter-http-health`,
`adapter-prom-slo`) are v1.1; v1 runs them through the pipeline runner's
mock adapter for dry runs. Plan accordingly: either wait for v1.1 real
adapters or ship the CD spec with a note that production enforcement
arrives in a follow-on."

Record: `policies.adapter_preferences`.

## Validation + freeze

1. Write the draft to `<target-repo>/.aieos/cd.spec.yaml`.
2. Run the pipeline runner's spec validator:
   ```bash
   aieos-pipeline-runner run --spec .aieos/cd.spec.yaml --expected-hash <sha> \
     --use-mock-adapters  # dry-run against mocks
   ```
3. Cross-validate against the CI spec — the CI's `publish.artifact`
   evidence must resolve to the same digest this CD's `artifact_ref`
   points at. See
   `aieos-governance-foundation/scripts/validate-spec-integrity.py`.
4. When green on mocks, freeze: commit `.aieos/cd.spec.yaml` and cache
   it in the artifact store keyed by the commit SHA.
5. Two-person sign-off per the framework's review rule.

The runner refuses unfrozen specs at execution time.
