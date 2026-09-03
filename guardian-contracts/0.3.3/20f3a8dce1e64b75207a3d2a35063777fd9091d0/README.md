# Guardian 0.3.3 contract snapshot

This directory is a read-only, integrity-bound snapshot of the review contract derived exclusively from private Guardian source commit `20f3a8dce1e64b75207a3d2a35063777fd9091d0`.

The private Guardian repository remains authoritative. This snapshot lets isolated external reviewers and Codex sessions construct and validate exact `OWNER_APPROVAL` evidence under `unico.guardian.review.v3` without private-source access. Approval evidence itself remains in the canonical evidence mechanism.

The snapshot grants no authority to approve, configure, operate, or change Guardian. It contains no credentials, secrets, private keys, installation tokens, runtime secret values, or executable private implementation.

Files:

- `review-v3.schema.json`: exact source schema.
- `owner-approval-contract.json`: machine-readable protected-policy and evaluator requirements.
- `evaluator-contract.md`: human-readable rejection and binding semantics.
- `manifest.json`: source/blob provenance and content hashes.
- `manifest.sha256`: SHA-256 of `manifest.json` (the manifest lists every other snapshot file, avoiding recursive self-hashing).
