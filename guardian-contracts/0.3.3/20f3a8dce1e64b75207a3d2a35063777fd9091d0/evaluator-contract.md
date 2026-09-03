# Guardian 0.3.3 Owner Approval evaluator contract

This document is a deterministic contract extraction from the private Guardian source at commit `20f3a8dce1e64b75207a3d2a35063777fd9091d0`. It is not executable Guardian code.

The review body must validate against `review-v3.schema.json` and contain exactly its 16 required fields. Legacy review schemas, missing fields, and additional fields are rejected. `OWNER_APPROVAL` requires `reviewer_role` `OWNER_REVIEWER`; missing, malformed, or unknown approval modes fail closed. The reviewer subject must be explicitly present in the protected reviewer registry with exactly `role`, `provenance`, and positive numeric `user_id`, with provenance `github-native`. Owner/Builder identity overlap is permitted only in Owner mode.

The record binds exactly to the protected and live target repository, PR number, HEAD SHA, base ref `main`, current base SHA, and current synthetic merge SHA. The PR must be open, unmerged, mergeable, and its HEAD must contain the current base. The verdict must be `APPROVE` and the review profile must be `guardian-v0.1`.

The evidence JSON contains no comment or issue ID. `evidence_reference` contains exactly `kind: github_issue_comment` and the protected evidence repository full name. Protected configuration supplies the dedicated repository numeric ID/full name, canonical issue database ID/number, and `review_records["<PR_NUMBER>"]` comment ID. The separately scoped evidence reader verifies all returned GitHub repository, issue, comment, author-login, and author-user-ID values. It rejects an edited comment unless `created_at == updated_at`, malformed or duplicate-key JSON, wrong location, deletion, or API unavailability.

Review JSON is canonically serialized with sorted keys, compact comma/colon separators, and ASCII escaping, then SHA-256 hashed. The digest must match the live comment. The final authorization binding includes the policy version, approval mode, exact release state, UUID authorization ID and nonce, review digest, full protected evidence provenance, trusted comment ID, and reviewer numeric user ID. Reusing an authorization ID with a different binding fails.

`issued_at`, `expires_at`, evidence `created_at`, trusted time, and CI timestamps are RFC3339 UTC strings ending in `Z`. Maximum clock skew is 300 seconds. Review lifetime must be positive and at most 691200 seconds. At evaluation, at least 605700 seconds must remain. Comment creation and review issuance may differ by no more than 300 seconds.

Required checks are `verify`, `scanner-contracts`, and `scanner-supabase-security`, each from GitHub App ID 15368 and bound to the exact PR HEAD. For each contract, the current run is the greatest `(created_at, run_id)` after identical duplicate IDs are collapsed. Conflicting duplicate IDs or malformed ordering fields fail closed. The selected run must be completed successfully and no more than 86400 seconds old (allowing the configured clock skew for future timestamps).

The public evidence repository remains untrusted input. This snapshot grants no approval authority and does not replace protected configuration, live GitHub readback, the private Guardian implementation, or the canonical evidence mechanism.
