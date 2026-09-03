# Guardian 0.3.5 contract snapshot

This directory is a read-only publication of the review and release-authorization contract derived from private Guardian source commit `21eb115375a3c1f63755e7bbd695b77743f065c0`. The private Guardian repository remains authoritative.

The snapshot lets isolated external reviewers construct and validate exact `OWNER_APPROVAL` evidence under `unico.guardian.review.v3`, and understand the fail-closed GitHub-state checks applied by Guardian 0.3.5, without access to private implementation source. Approval evidence remains solely in the canonical evidence mechanism.

Guardian 0.3.5 keeps policy `unico.guardian.policy.v0.3`, review schema `unico.guardian.review.v3`, and the existing `OWNER_APPROVAL` / `INDEPENDENT_APPROVAL` semantics. The schema copy is byte-identical to the authoritative v3 source blob.

Contract carried forward from 0.3.4:

- Check Suites are completely enumerated for the exact target HEAD, then every suite's Check Runs are completely enumerated with `filter=all`, within hard pagination and item bounds.
- Authorization-relevant Check Runs are reread by exact ID between two complete membership snapshots. Incomplete, unstable, duplicate, malformed, or out-of-bound provider state fails closed.
- A required check matches only its exact name, protected App ID, and exact HEAD. The current run is the greatest `(started_at, CheckRun ID)`. `created_at` is not used; `completed_at` is used for freshness, not current-run ordering.
- Trust-relevant numeric GitHub identities require canonical exact integer types and their valid positive or non-negative domains.
- Before PASS side effects, selected required Check Runs are reread by exact ID and the repository, PR, base, merge, lifecycle, mergeability, and base-containment state is reread and compared.
- PASS is an attestation of the exact state observed and revalidated at that point in time; it is not a claim that GitHub or provider state will remain immutable afterward.

Fail-closed correction in 0.3.5:

- Immediately after strict JSON parsing, the Evidence Reader validates the complete review-v3 object: exact object shapes and field sets, exact JSON types, constants, enums, patterns, UUID strings, positive integer domains, and date-time strings.
- `authorization_id` and `nonce` must be JSON strings in hyphenated UUID form. Integer, float, boolean, null, array, object, malformed string, and non-hyphenated string representations are rejected; no `str(...)` or other coercion can make them usable.
- Schema-invalid evidence is rejected before canonical review digest construction, replay-ledger access, authorization/audit binding, or any Guardian CheckRun publication. Direct evaluator invocation applies the same validator as defense in depth.
- The immutable review-v3 schema was not weakened or relabeled; Guardian 0.3.4 remains historical behavior and 0.3.5 closes its runtime differential.

Files:

- `review-v3.schema.json`: byte-exact authoritative schema copy.
- `owner-approval-contract.json`: machine-readable policy, evidence, collection, selection, and final-guard contract.
- `evaluator-contract.md`: human-readable evaluator/runtime contract and rejection semantics.
- `manifest.json`: complete six-file inventory plus source and deployment provenance.
- `manifest.sha256`: SHA-256 values for every other artifact, including `manifest.json`.

The integrity root does not hash itself. Internal hashes establish consistency only. A detached directory cannot establish its own origin; authenticity derives from the immutable Git commit/tree containing this snapshot or an equivalent trusted external signature.

This snapshot grants no authority to approve, configure, operate, or change Guardian. It contains no credentials, secrets, private keys, tokens, writable capability, executable Guardian implementation, or unrelated private source.
