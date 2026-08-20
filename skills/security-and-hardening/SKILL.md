---
name: security-and-hardening
description: Hardens code that handles untrusted input, authentication, sensitive data, external integrations, file or command execution, deployment permissions, or tenant boundaries.
---

# Security and Hardening

Review the changed surface proportionately. Security controls must follow an identified trust boundary or abuse case; generic hardening is not a substitute for a threat model.

## Threat model first

1. **Boundaries:** where external data or authority enters—requests, forms, files, webhooks, APIs, queues, repository content, tool arguments, and LLM output.
2. **Assets:** credentials, PII, tenant data, admin actions, money movement, integrity, and availability.
3. **Abuse cases:** how an attacker could spoof identity, tamper with data, deny an action, disclose information, exhaust resources, or gain privilege.
4. **Controls and evidence:** place the smallest effective control at the boundary and test the abuse case.

## Review priorities

Inspect only relevant categories:

- authentication, authorization, ownership, and tenant isolation;
- secrets, PII, sensitive output, logging, and error disclosure;
- query, command, template, HTML, path, and file-execution injection;
- server-side URL fetching, redirects, network allowlists, and external integrations;
- input shape and aggregate size, rate, timeout, recursion, and cost bounds;
- session and browser controls when the application actually uses them;
- dependency, lockfile, install-script, CI, deployment, and permission changes;
- LLM output handling, prompt injection, retrieval isolation, and tool authority.

Treat framework defaults as evidence to verify, not guarantees. Treat model output as untrusted data. Enforce permissions in code rather than prompts or client-side checks.

## Decision rules

- Validate untrusted input at its system boundary and encode output for its destination.
- Parameterize queries; construct commands and paths from allowlisted operations rather than concatenated input.
- Authorize each protected operation against the target resource, not only the route or session.
- Keep secrets out of source, logs, responses, prompts, and client-accessible storage.
- Bound attacker-controlled work across individual and aggregate requests.
- Require explicit approval for new auth flows, sensitive-data categories, privileged roles, external integrations, file uploads, or relaxed CORS/rate limits.
- Rotate any exposed credential before history cleanup.
- Do not apply forced dependency remediation without reviewing compatibility and reachability.

## Dependency review

Find the installation boundary and authoritative package manager from the owning lockfile, manifest metadata, and CI. Stop on disagreement or competing lockfiles. Use frozen or immutable installs, block unreviewed dependency scripts, inspect new dependency and lockfile diffs together, and triage advisories by reachability and deployment context.

A passing audit covers known advisories only; it does not prove provenance, maintainer trust, or script safety.

## Severity and remediation

Prioritize reachable impact:

- **Critical:** reachable code execution or injection, authorization bypass, cross-tenant disclosure, exposed secrets, or destructive integrity/data-loss risk. Fix before release.
- **High:** practical compromise with meaningful confidentiality, integrity, or availability impact. Fix promptly or document a time-bounded mitigation.
- **Moderate/low:** track according to reachability and environment; avoid risky upgrades whose remediation cost exceeds the exposed risk.

For each finding report the boundary, exploit or failure path, impact, evidence, smallest safe fix, and residual risk.

## On-demand references

Read only the references relevant to the changed surface:

- `references/web-patterns.md` — input, auth, browser, upload, SSRF, rate-limit, and secret patterns.
- `references/supply-chain.md` — package-manager discovery, install-script policy, audits, and provenance.
- `references/ai-llm.md` — prompt injection, model output, tool authority, retrieval, and consumption.
- `references/security-checklist.md` — final review and verification checklist.

## Verification

Run focused abuse-case tests plus applicable checks from `references/security-checklist.md`. Report commands and observed results; do not infer runtime safety from source inspection alone.
