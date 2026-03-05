# ANONYMIZATION

This document defines the **mandatory anonymization rules** for all content in the **aieos-release-exposure-kit** repository.

The goal is to ensure this repository remains:
- Publicly shareable
- Employer-neutral
- Tool-agnostic
- Safe for reuse and contribution
- Suitable for AI-assisted generation and validation

These rules apply to **all files**, including examples, templates, validators, diagrams, and documentation.

---

## Core Rule

> **No content may reference a real employer, internal system, or proprietary environment.**

If there is any doubt, **anonymize**.

---

## Prohibited Content (Must NOT Appear)

### Employer & Organization Identifiers
- Company names (current or former)
- Business unit names
- Internal team names
- Employer-specific acronyms
- Internal program or initiative names

### Internal Systems & Tooling
- Internal platform names
- Proprietary service names
- Internal dashboards or portals
- Internal CI/CD system names
- Internal ticketing or change-management systems
- Internal observability or monitoring system names
- Internal feature flag system names

### Identifiers & Metadata
- Internal URLs or domains
- IP addresses (internal or external)
- Email addresses tied to real organizations
- Real usernames or personal names
- Ticket IDs or prefixes tied to a real system
- Incident numbers
- Change request IDs
- Cluster or environment identifiers tied to a real org
- Real release authorization references

### Configuration & Secrets
- API keys or tokens (even redacted)
- Certificates
- Credentials
- Environment-specific secrets
- Real account, subscription, or tenant IDs

### Logs, Screenshots, and Output
- Screenshots containing identifiers
- Logs with timestamps, IDs, or hostnames tied to a real environment
- Command output revealing environment details

---

## Required Anonymization Patterns

When examples require realism, use **explicit placeholders**.

### Person Names
Use clearly fictional names consistently throughout examples. The same fictional names may be reused across examples to demonstrate role continuity:
- Release owners: e.g., "Sarah Chen," "Marcus Rivera"
- Engineering managers: e.g., "David Park"
- SRE contacts: e.g., "Marcus Rivera"

Do not use real person names, real initials, or names that might identify a real individual.

### Organization and Project Names
Use clearly fictional project/organization names:
- `TaskFlow` — the example SaaS product used throughout this repository's worked examples

Do not use:
- Real company names
- Real product names
- Internal initiative names

### Approved Placeholder Conventions

Use ALL CAPS placeholders where specific values are not yet known:

- `ORG_NAME`
- `TEAM_NAME`
- `SERVICE_NAME`
- `SYSTEM_NAME`
- `REPO_NAME`
- `CLUSTER_NAME`
- `ENV_NAME`
- `FEATURE_NAME`

### Domains & URLs
Use:
- `example.com`
- `api.example.com`
- `app.example.com`

Never use:
- Real company domains
- Personal domains

### Monitoring and Tooling References
Examples may reference generic monitoring systems by category:
- "Datadog" — an accepted commercial reference (tool-agnostic templates use "{monitoring system}" placeholder)
- "LaunchDarkly" — an accepted commercial reference (tool-agnostic templates use "{feature flag system}" placeholder)
- "kubectl" — an accepted open-source tool reference

When referencing commercial tools in examples, do so to illustrate the pattern — not to endorse a specific tool. Templates must use generic placeholders (not tool names).

### Environments
Use generic names only:
- `dev`
- `staging`
- `preprod`
- `prod`

Avoid:
- Internally meaningful environment names
- Region- or datacenter-specific labels

### Feature Flag Names
Examples use the `{FEATURE}_{BEHAVIOR}` naming convention defined in the RCF:
- `NOTIF_ENABLED` — acceptable example flag name
- `NOTIF_BATCH_SIZE` — acceptable example flag name

Do not use real feature flag names from production systems.

---

## Release Record Evidence References

The Release Record requires retrievable evidence locations. In examples, use fictional but plausible paths:
- `releases/RR-{PROJECT}-{NNN}/deploy-step1.log` — acceptable
- Internal URLs tied to a real organization — prohibited

---

## Checklist for Contributors

Before submitting a PR, verify:

- [ ] No company names, internal team names, or employer-specific acronyms appear
- [ ] No internal URLs, domains, or ticketing identifiers appear
- [ ] All person names are clearly fictional
- [ ] All system and service names are either fictional (TaskFlow) or clearly generic
- [ ] Log excerpts and command output do not expose real environment details
- [ ] Feature flag names follow the `{FEATURE}_{BEHAVIOR}` convention and are clearly fictional
- [ ] Templates use generic placeholders, not tool-specific names
