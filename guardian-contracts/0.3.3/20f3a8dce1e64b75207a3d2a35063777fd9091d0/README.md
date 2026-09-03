# Guardian 0.3.3 contract snapshot

This directory is a read-only snapshot of the review contract. Its source-derived contract material comes exclusively from private Guardian source commit `20f3a8dce1e64b75207a3d2a35063777fd9091d0`. Concrete evidence repository and issue numeric IDs are separately classified as deployment-specific GitHub API metadata.

The private Guardian repository remains authoritative. This snapshot lets isolated external reviewers and Codex sessions construct and validate exact `OWNER_APPROVAL` evidence under `unico.guardian.review.v3` without private-source access. Approval evidence itself remains in the canonical evidence mechanism.

The snapshot grants no authority to approve, configure, operate, or change Guardian. It contains no credentials, secrets, private keys, installation tokens, runtime secret values, or executable private implementation.

Files:

- `review-v3.schema.json`: exact source schema.
- `owner-approval-contract.json`: machine-readable protected-policy and evaluator requirements.
- `evaluator-contract.md`: human-readable rejection and binding semantics.
- `manifest.json`: complete six-file inventory, source/blob provenance, deployment-metadata provenance, and payload hashes.
- `manifest.sha256`: conventional integrity-root checksum file containing SHA-256 values for every other snapshot file, including `manifest.json`.

The integrity root does not hash itself. Its authenticity comes from the exact immutable Git commit/tree containing this directory. A copied directory without that trusted Git commit or an equivalent external signature can verify internal checksum consistency, but cannot independently establish origin. This non-circular model makes no self-authentication claim.
