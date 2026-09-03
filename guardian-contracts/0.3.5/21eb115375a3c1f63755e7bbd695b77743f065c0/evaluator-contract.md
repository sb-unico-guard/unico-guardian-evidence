# Guardian 0.3.5 Owner Approval evaluator contract

This document is a deterministic, non-executable contract extraction from private Guardian source commit `21eb115375a3c1f63755e7bbd695b77743f065c0`.

## Stable authoritative GitHub collection

Guardian collects against the exact target HEAD. It completely enumerates Check Suites from the exact-HEAD commit endpoint, then completely enumerates every suite's Check Runs using `filter=all`. Pagination is counted and bounded: page size 100, at most 999 suites, 999 runs per suite, and 999 total runs.

Each suite and run must carry canonical positive integer IDs and must bind to the expected HEAD, suite, and App identities. Duplicate suite IDs and duplicate CheckRun IDs anywhere in the live snapshot are rejected. Malformed counts, changing totals, incomplete pages, exhausted bounds, or any inability to prove complete collection yields a fail-closed unavailable result and cannot authorize PASS.

Collection uses a bounded A / exact-reread / B protocol:

1. collect complete membership snapshot A;
2. reread every authorization-relevant run by exact CheckRun ID and require its normalized authorization fields to match A;
3. collect complete membership snapshot B;
4. require A and B membership to be identical and require all relevant normalized records to remain identical.

Authorization-relevant fields are CheckRun ID, name, App ID, suite ID, HEAD SHA, status, conclusion, `started_at`, and `completed_at`.

## Required-check selection and freshness

The protected required checks remain `verify`, `scanner-contracts`, and `scanner-supabase-security`, each from GitHub Actions App ID 15368 and the exact PR HEAD.

For each required contract, candidates are restricted to exact name + exact App ID + exact HEAD. The current authoritative run is the greatest deterministic tuple `(started_at, CheckRun ID)`. `started_at` must be valid RFC3339 UTC. `created_at` is not collected or used for ordering. `completed_at` does not select the current run; it must be valid and is used only to enforce completed-success state and the 86400-second freshness limit. Conflicting or unresolvable identity/order state fails closed.

## Canonical numeric identity boundary

Authorization-relevant GitHub numeric identities must have exact integer type: booleans, floats (including integral floats), numeric strings, null, arrays, and objects are not integers. IDs must be positive; provider collection counts may be zero but otherwise must be canonical non-negative integers.

This applies at the relevant config/provider/evidence boundaries to repository, PR, installation, App, reviewer-user, suite, CheckRun, issue, comment, and protected review-record identities.

## Evidence trust boundary

The review body must validate against the byte-exact `review-v3.schema.json` contract and contain exactly its 16 required fields. Legacy schemas, missing fields, extra fields, duplicate JSON keys, malformed JSON, wrong author/location, edited comments, and unavailable provenance fail closed.

Immediately after strict JSON parsing and before the review is trusted, the Evidence Reader applies one complete review-v3 validation boundary. The top-level review, `target_repository`, and `evidence_reference` must be exact JSON objects with exactly the schema fields. Every schema string, integer, constant, enum, pattern, UUID format, positive domain, and RFC3339 date-time format is checked with no implicit coercion. The evaluator applies the same validator for direct or alternate invocation.

Both `authorization_id` and `nonce` must be JSON strings in standard hyphenated UUID text form. Hexadecimal letter case is not semantically normalized for binding: the original valid string remains part of the canonical review digest. Integer (even when `str(integer)` is UUID-parseable), float, boolean, null, array, object, malformed string, and non-hyphenated string values are schema-invalid and rejected.

A schema-invalid value raises a trust-boundary error before protected identity comparison, canonical review digest construction, evaluator authorization, replay-ledger access, authorization/audit binding, or Guardian CheckRun publication. Valid canonical evidence remains eligible for ordinary evaluation; a legitimate policy or CI failure from valid evidence may still publish a normal Guardian FAIL.

The evidence JSON contains no comment or issue ID. `evidence_reference` contains exactly `kind: github_issue_comment` and the protected evidence repository name. Protected configuration supplies repository ID/name, canonical issue ID/number, and `review_records["<PR_NUMBER>"]`. The Evidence App read path verifies the returned repository, issue, comment, author login, author numeric ID, and `created_at == updated_at`.

Review JSON is serialized with sorted keys, compact comma/colon separators, ASCII escaping, and SHA-256. The authorization binding includes policy version, approval mode, exact release tuple, authorization UUID, nonce, review digest, protected evidence provenance, trusted comment ID, and reviewer numeric user ID. Conflicting replay binding fails.

## Approval and timing

`OWNER_APPROVAL` requires an explicitly protected `OWNER_REVIEWER` with `github-native` provenance; builder overlap is permitted only in this mode. `INDEPENDENT_APPROVAL` retains its different-real-principal requirement and rejects reviewer/builder overlap. Missing or unknown approval mode blocks.

The review binds exact target repository, PR number, HEAD, base ref `main`, current base SHA, synthetic merge SHA, reviewer identity/role, profile `guardian-v0.1`, verdict, timestamps, evidence provenance, and CI state. The PR must be open, non-draft, unmerged, mergeable, and its HEAD must contain the current base.

Maximum clock skew is 300 seconds. Review lifetime must be positive and at most 691200 seconds; at least 605700 seconds must remain at evaluation. Comment creation and review issuance may differ by no more than 300 seconds.

## Final PASS guard and point-in-time meaning

After an evaluator PASS candidate and before any PASS binding or publication, Guardian rereads every selected required CheckRun by exact ID, validates its suite/App/HEAD identity, and requires all normalized authorization fields to remain unchanged. It then rereads the repository, PR, current base ref, merge SHA, lifecycle/draft/merged/mergeable state, and base containment, requiring exact equality with the evaluated observation.

Only after these comparisons may Guardian create the authorization binding and publish PASS. A drift or unavailable reread produces no PASS-side write. PASS describes the exact state verified at that point in time and does not assert that GitHub/provider state remains immutable afterward.

## Protected Guardian check collision

The protected Guardian check is `unico-independent-release-guardian/v0.1` from App ID 4772439. The same protected name observed from any different App ID blocks authorization with reason `guardian_check.source_collision`.

The public evidence repository remains untrusted input. This snapshot grants no approval authority and does not replace protected configuration, live GitHub readback, the private implementation, or canonical evidence handling.
