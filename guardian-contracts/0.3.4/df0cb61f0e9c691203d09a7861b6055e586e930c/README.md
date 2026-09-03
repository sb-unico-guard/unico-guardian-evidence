# Guardian 0.3.4 contract snapshot

This directory is a read-only publication of the review and release-authorization contract derived from private Guardian source commit `df0cb61f0e9c691203d09a7861b6055e586e930c`. The private Guardian repository remains authoritative.

The snapshot lets isolated external reviewers and Codex sessions construct and validate exact `OWNER_APPROVAL` evidence under `unico.guardian.review.v3`, and understand the fail-closed GitHub-state checks applied by Guardian 0.3.4, without access to private implementation source. Approval evidence remains solely in the canonical evidence mechanism.

Guardian 0.3.4 keeps policy `unico.guardian.policy.v0.3`, review schema `unico.guardian.review.v3`, and the existing `OWNER_APPROVAL` / `INDEPENDENT_APPROVAL` semantics. The schema copy is byte-identical to the authoritative v3 source blob.

Contract additions in 0.3.4:

- Check Suites are completely enumerated for the exact target HEAD, then every suite's Check Runs are completely enumerated with `filter=all`, within hard pagination and item bounds.
- Authorization-relevant Check Runs are reread by exact ID between two complete membership snapshots. Incomplete, unstable, duplicate, malformed, or out-of-bound provider state fails closed.
- A required check matches only its exact name, protected App ID, and exact HEAD. The current run is the greatest `(started_at, CheckRun ID)`. `created_at` is not used; `completed_at` is used for freshness, not current-run ordering.
- Trust-relevant numeric GitHub identities require canonical exact integer types and their valid positive or non-negative domains.
- Non-canonical review target-repository or pull-request identities are rejected in the Evidence Reader before review digest construction, evaluator authorization, replay/audit binding, or Guardian check publication.
- Before PASS side effects, selected required Check Runs are reread by exact ID and the repository, PR, base, merge, lifecycle, mergeability, and base-containment state is reread and compared.
- PASS is an attestation of the exact state observed and revalidated at that point in time; it is not a claim that GitHub or provider state will remain immutable afterward.

Files:

- `review-v3.schema.json`: byte-exact authoritative schema copy.
- `owner-approval-contract.json`: machine-readable policy, evidence, collection, selection, and final-guard contract.
- `evaluator-contract.md`: human-readable evaluator/runtime contract and rejection semantics.
- `manifest.json`: complete six-file inventory plus source and deployment provenance.
- `manifest.sha256`: SHA-256 values for every other artifact, including `manifest.json`.

The integrity root does not hash itself. Internal hashes establish consistency only. A detached directory cannot establish its own origin; authenticity derives from the immutable Git commit/tree containing this snapshot or an equivalent trusted external signature.

This snapshot grants no authority to approve, configure, operate, or change Guardian. It contains no credentials, secrets, private keys, tokens, writable capability, executable Guardian implementation, or unrelated private source.
