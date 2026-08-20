# Web Security Patterns

Load this reference when the changed surface includes HTTP input, sessions, browser output, uploads, or server-side URL fetching. Adapt controls to the repository's framework and deployment model.

## Input and output

- Parse and validate request bodies, parameters, headers, and webhook payloads at entry.
- Constrain type, format, length, nesting, count, and aggregate payload size.
- Parameterize database queries.
- Encode output for HTML, URL, JavaScript, CSS, shell, template, or log context.
- Prefer framework auto-escaping; sanitize only when intentionally rendering trusted HTML subsets.
- Return generic client errors and retain sensitive diagnostics only in protected logs.

## Authentication and sessions

- Use established password hashing such as Argon2id, scrypt, or bcrypt with repository-appropriate parameters.
- Store session identifiers in `HttpOnly`, `Secure`, appropriately scoped `SameSite` cookies when cookie sessions are used.
- Rotate sessions after authentication and privilege changes; expire reset and verification tokens.
- Rate-limit authentication and recovery paths using an identity-aware and network-aware strategy.

## Authorization

Authentication proves identity; authorization must check the requested operation against the target resource. Test horizontal access, vertical privilege escalation, identifier substitution, bulk endpoints, and indirect object references. Enforce tenant scoping in the data-access boundary where possible.

## Browser boundaries

- Use a restrictive Content Security Policy suited to actual asset sources.
- Set HSTS only for HTTPS deployments that can safely enforce it.
- Prevent framing unless embedding is intentional.
- Restrict CORS to required origins, methods, headers, and credential behavior.
- Do not store bearer credentials in browser storage when a safer server-managed session is available.

## File uploads

Constrain size, count, extension, declared MIME type, and verified content signature. Generate server-side names, store outside executable/static paths, prevent path traversal and overwrite, and scan or isolate risky formats according to exposure. Treat archive expansion and media processing as resource and parser boundaries.

## Server-side URL fetching

Prefer fixed destinations or host allowlists. Require an expected scheme, reject credentials and unsafe ports, resolve all addresses, reject private/reserved/link-local destinations, and control redirects. A preflight DNS check alone has a rebinding gap because the HTTP client may resolve again; high-risk fetchers need pinned resolution or an egress-filtering transport.

## Rate and resource limits

Apply limits to the expensive operation, not merely the outer route. Bound request size, concurrency, downstream retries, pagination, decompression, regex work, recursion, generated tokens, and external-call duration. Verify failure behavior under cancellation and timeout.

## Secrets

Load secrets through the deployment's secret mechanism. Keep placeholders—not values—in examples. If a secret reaches version control, logs, prompts, build artifacts, or an untrusted client, revoke and rotate it before cleanup.
