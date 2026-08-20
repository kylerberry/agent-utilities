# AI and LLM Security

Load this reference when an application sends untrusted content to a model, consumes model output, performs retrieval, or gives an agent tools.

## Prompt injection

Assume any user message, fetched page, document, repository file, tool result, or retrieved record can contain hostile instructions. A system prompt is policy guidance, not an authorization boundary. Keep permissions, tenant checks, and destructive-action approval in deterministic code.

## Model output

Treat output as untrusted data:

1. Parse into a narrow data shape.
2. Validate schema, values, size, and allowed operations.
3. Encode for the destination.
4. Execute only allowlisted actions with independently authorized arguments.

Do not pass raw output to SQL, a shell, `eval`, HTML sinks, file paths, templates, or privileged tools.

## Sensitive context

Keep secrets, credentials, unnecessary PII, cross-tenant data, and privileged system details out of prompts and retrieval results. Assume supplied context may be reproduced in output or retained by an external provider according to its service terms.

## Tool authority

Grant the smallest tool and resource scope. Validate every argument at the tool boundary. Require explicit human confirmation for destructive, irreversible, financial, publication, privilege, or release actions. Bound loops, retries, delegation depth, wall-clock time, and spend.

## Retrieval

Partition indexes and filters by tenant or authorization domain. Recheck authorization when retrieving and when using a retrieved record. Validate content before indexing, retain source provenance, and treat retrieved instructions as data rather than policy.

## Consumption and denial of service

Cap request size, retrieved context, generated tokens, parallel calls, retries, tool calls, recursion, and aggregate user or tenant spend. Test timeout, cancellation, partial-output, and provider-failure behavior.

## Evidence

Security tests should cover hostile prompt content, malformed model output, unauthorized tool arguments, cross-tenant retrieval attempts, secret-exfiltration requests, and consumption limits where applicable.
