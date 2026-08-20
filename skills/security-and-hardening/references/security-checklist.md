# Security Review Checklist

Use only applicable sections. Record evidence or non-applicability rather than checking boxes from source inspection alone.

## Boundaries and authorization

- [ ] Changed trust boundaries and protected assets are identified.
- [ ] Untrusted input is validated at entry with shape and resource limits.
- [ ] Protected operations authorize the actor against the target resource.
- [ ] Tenant isolation is enforced and tested where applicable.
- [ ] Abuse cases map to controls and focused tests.

## Injection and output

- [ ] Queries are parameterized.
- [ ] Commands, paths, templates, and redirects use constrained construction.
- [ ] Output is encoded for its destination.
- [ ] Server-side URL fetching prevents access to unintended networks and unsafe redirects.
- [ ] Uploads constrain content, storage location, names, expansion, and processing.

## Data and credentials

- [ ] No secrets appear in source, staged changes, logs, prompts, responses, or client storage.
- [ ] Sensitive fields are minimized in storage and output.
- [ ] Error responses avoid internal details.
- [ ] Exposed credentials are revoked and rotated.

## Sessions and browser

- [ ] Session cookies and token lifecycle match the deployment threat model.
- [ ] Login, recovery, and expensive endpoints have appropriate rate limits.
- [ ] CORS, CSP, framing, transport, and browser-storage controls are appropriate.

## Supply chain and deployment

- [ ] One authoritative lockfile and package manager govern the install boundary.
- [ ] CI uses a frozen or immutable install.
- [ ] Dependency scripts are blocked or specifically approved.
- [ ] New dependencies, lockfile changes, advisories, and provenance were reviewed together.
- [ ] CI, deployment, secrets, and permission changes follow least privilege.

## AI and agents

- [ ] Model and retrieved output is treated as untrusted.
- [ ] Secrets and cross-tenant data are excluded from prompts and retrieval.
- [ ] Tool arguments are validated and independently authorized.
- [ ] Destructive or consequential actions require appropriate confirmation.
- [ ] Tokens, calls, retries, recursion, and spend are bounded.

## Verification evidence

- [ ] Focused abuse-case tests pass.
- [ ] Relevant type checks, lint, build, and integration tests pass.
- [ ] Runtime controls were observed where source inspection is insufficient.
- [ ] Dependency audit findings were triaged by reachability and remediation risk.
- [ ] Residual risks and deferred findings have an owner or durable tracking location.
