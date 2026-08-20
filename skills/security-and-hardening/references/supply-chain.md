# Supply-Chain Review

## Find the installation boundary

1. Locate the workspace root that owns the authoritative lockfile.
2. Corroborate the package manager and version using manifest metadata, lockfile format, and CI.
3. Stop when these disagree or competing lockfiles govern one installation boundary.
4. Use that manager's frozen or immutable install mode in CI.

Do not assume npm or treat the nearest manifest as the install root.

## Install scripts

Block dependency scripts before first execution when the ecosystem supports it. Inspect pending script source and approve only packages that require it. Commit the policy and verify it with a clean immutable install. Never blanket-approve scripts.

## New dependencies

Review the manifest and lockfile diff together:

- exact package identity and typosquat risk;
- maintainer and ownership changes;
- release age and cadence;
- registry provenance or signatures where supported;
- runtime versus development reachability;
- transitive dependency growth;
- native binaries, post-install scripts, network access, and build-time authority.

Missing provenance is an investigation signal, not proof of compromise.

## Audit triage

A package-manager audit finds known advisories; it does not establish that vulnerable code is reachable or that an unflagged package is trustworthy.

For each advisory determine:

1. Is the vulnerable component present in the deployed, build, test, or release path?
2. Is the vulnerable function reachable under the deployment configuration?
3. Is a compatible fix available?
4. Does remediation introduce a major-version or transitive supply-chain risk?
5. What mitigation and review date apply if deferred?

Fix reachable critical/high issues promptly. Track unreachable or development-only findings with evidence rather than silently dismissing them.

Never run forced remediation such as `npm audit fix --force` automatically. Preview changes, read relevant release notes, and test resulting upgrades.
