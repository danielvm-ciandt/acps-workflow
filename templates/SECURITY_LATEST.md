# Security Guardrails — [Project Name]

> Immutable per snapshot. AI agents cannot deviate from policies listed here without explicit human approval. Snapshotted on each update — the dated file is the auditable record.

## Authentication & Authorization

[Auth patterns required: session handling, token lifetimes, RBAC requirements]

## Data Handling

[PII classification, encryption at rest/in transit, retention policies, anonymization rules]

## Dependency Policy

[Approved registries, prohibited packages, vulnerability scan requirements, license restrictions]

## Secret Management

[How secrets are stored and accessed — never committed, required env vars, vault/secret manager in use]

## Compliance Requirements

[Regulatory frameworks: GDPR, HIPAA, SOC 2, PCI-DSS, etc. — state what applies and key obligations]

## Forbidden Patterns

[Code patterns the AI must never introduce — e.g. hardcoded credentials, eval(), unsafe SQL interpolation]

## Change History

| Version | Date | Summary |
|---------|------|---------|
| Initial | [date] | Created by speckit.acps.setup |
