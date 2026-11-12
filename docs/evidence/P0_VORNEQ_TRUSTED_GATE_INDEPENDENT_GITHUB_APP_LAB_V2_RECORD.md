# P0 VORNEQ — Trusted Gate V2 Source-Pinning Laboratory Record

**Document ID:** `P0_VORNEQ_TRUSTED_GATE_INDEPENDENT_GITHUB_APP_LAB_V2`  
**Date:** 2026-11-12 (UTC)  
**Repository:** `13640226/vorneq-security-lab-branch-protection`  
**Documentation-only record; no implementation or activation authority**  
**Production baseline (observed before record):** `13640226/VORNEQ@cbb6e687366ccda07b8b81507bf68f753606f3e6`  
**Related evidence:** [Laboratory Issue #4](https://github.com/13640226/vorneq-security-lab-branch-protection/issues/4#issuecomment-6811754761) and [VORNEQ Issue #581](https://github.com/13640226/VORNEQ/issues/581).

## 1. Scope and provenance

This file **records** the design, offline implementation evidence, and remaining provider-test gaps of the Independent GitHub App Trusted Gate V2. It does not publish, enable or approve a GitHub App, modify a Ruleset, install secrets, or assert that the security control works against live GitHub source spoofing.

The complete prototype is supplied separately as a conversation-attached archive named `VORNEQ_TRUSTED_GATE_APP_SOURCE_PIN_V2.zip`. **The code archive is not included in this GitHub commit.** This record must not be misrepresented as a code implementation commit.

**Archive SHA-256 (independently rechecked in the isolated session container):**

```text
983c75ee1dac01d47918042357f7e000adeffec7e1f925436daf85e3b6323b87  VORNEQ_TRUSTED_GATE_APP_SOURCE_PIN_V2.zip
```

## 2. Contents of the archive

- `src/trusted_gate.py`: baseline signed offline CI/owner authorization evaluation.
- `src/github_app_gate.py`: RSA GitHub App JWT and installation-token prototype, PR/SHA validation, mocked Check Run publication and source-pin evaluation.
- `src/lab_app_cli.py`: lab-only CLI; publication opt-in and production repository excluded by code.
- `tests/test_github_app_gate.py`: mocked API, identity, race, status spoof and signed-attestation tests.
- `docs/ARCHITECTURE.md`: trust-boundary design and limits.
- `docs/APP_REGISTRATION_AND_TEST_RUNBOOK.md`: account-level App registration, installation and provider test procedure.
- `docs/REAL_SRC10_TEST_MATRIX.csv`: **unexecuted** real-App negative-test matrix.
- `evidence/results.json`, `evidence/offline_unittest.log`, `SHA256SUMS.txt`: local-test evidence and archive manifest.

Static inspection found no private key or GitHub token matching the searched credential patterns. This is a limited scan, **not** an exhaustive independent supply-chain review.

## 3. Actual offline result

```text
TEST_COMMAND=PYTHONPATH=src python -m unittest discover -s tests -q
ACTUAL_TESTS=47
ACTUAL_FAILURES=0
ACTUAL_ERRORS=0
ACTUAL_RESULT=PASS
TEST_ENVIRONMENT=ISOLATED_OFFLINE_SESSION
GITHUB_API=MOCKED
SIGNING_KEYS=SYNTHETIC_EPHEMERAL
REAL_APP_PUBLISHING=NOT_EXECUTED
```

This 47/47 offline result was re-run during this registration. It is **not** equivalent to a GitHub provider-level negative test.

## 4. Design and intended trust boundary

- A *separate* GitHub App may publish `vorneq-lab/trusted-gate-v2` **Check Runs** only on this lab repository.
- GitHub source pinning must use the *actual installed App ID*, not GitHub Actions App ID `15368`.
- The private RSA key must never be in an ordinary repository secret or any PR-readable execution context. Prefer independent key storage and publisher execution.
- App installation permissions: least-privilege `Checks: read/write`, `Contents: read`, `Pull requests: read`, `Issues: read` and mandatory metadata read. No `statuses:write`, `administration:write`, deploy or secrets permissions.
- The evaluator must bind authorization to PR number, target branch and exact current Head SHA; reject revoked approvals, duplicate open head SHAs, stale data, skipped/neutral/missing CI and untrusted attestation sources.
- A real independent CI executor and authentic signed attestation are **separate** prerequisites. A successful App Check Run alone does not prove correct execution of CI.

## 5. Existing provider evidence (before V2)

Prior lab [Issue #4](https://github.com/13640226/vorneq-security-lab-branch-protection/issues/4) records:

| Finding | Real GitHub result |
|---|---|
| SRC-09 | Same-repository PR sees synthetic repository-secret canary; main-only Environment canary blocked from PR |
| SRC-10 | Later untrusted `success` overwrites prior owner `failure` on same commit-status context/SHA |
| PERM-08 | Status write HTTP 403 for read-only push token; HTTP 201 when `statuses:write` explicitly granted |
| BP-SHA-01 | Strict required-check Rule denied merge HTTP 405; after Update Branch, new SHA/CI permitted synthetic lab merge |
| Recovery | Lab direct push rejected HTTP 409; owner disabled lab Ruleset with empty bypass list |

**These results reproduce threats and GitHub behavior. They do not close SRC-09 or SRC-10 remediation.**

## 6. Remaining mandatory provider tests

1. Create and install a real, separate GitHub App **only** on `13640226/vorneq-security-lab-branch-protection`.
2. Hold App credentials outside PR-controlled GitHub Actions and outside publicly readable repository files; never publish PEM, installation token, or canary value.
3. Publish one real App-owned lab Check Run; capture `check_run.app.id`, `head_sha`, `conclusion`, installation identity and source-restricted permission.
4. Pin a **lab-only** required Check context to that actual new App ID; verify a forged same-name `success` from `github-actions[bot]` (App ID 15368) cannot satisfy it, with actual GitHub merge denial evidence.
5. Execute independent trusted CI and verify signed attestation authenticity, non-replay, current SHA and race behavior.
6. Verify positive success on a new authorized synthetic SHA.
7. Verify isolated test-account recovery, then disable lab Rule and Actions and remove all synthetic test keys/tokens.

Do not reuse the original VORNEQ production credentials or install the experimental App on production.

## 7. Documentary decision

```text
DOCUMENTARY_REGISTRATION=ACCEPTED_FOR_RECORD
OFFLINE_APP_ADAPTER_TESTS=47/47_PASS
ORIGINAL_GITHUB_APP_REGISTERED=NO
REAL_LAB_GITHUB_APP_INSTALLED=NO
REAL_APP_CHECK_RUN_PUBLISHED=NO
REAL_APP_SOURCE_PIN_NEGATIVE_TEST=NOT_EXECUTED
INDEPENDENT_CI_ATTESTATION=NOT_IMPLEMENTED
BP_SRC_02=OPEN_CRITICAL
SRC_09=OPEN_CRITICAL
SRC_10=OPEN_CRITICAL
PERM_08=RISK_REPRODUCED
LAB_RULESET_27082568=DISABLED_AT_LAST_CHECK
LAB_ACTIONS=DISABLED_AFTER_PREVIOUS_TESTS
VORNEQ_MAIN_MUTATION=NONE
PRODUCTION_BRANCH_PROTECTION_GO=NO
EXECUTION_AUTHORIZATION_FOR_PRODUCTION=NONE
FINAL_STATE=OFFLINE_V2_RECORDED__REAL_APP_PENDING__PRODUCTION_NO_GO
```

**No claim of independent third-party review or live security closure.**

---
*Record-only; see Issue #4 for real provider test run URLs and the current evidence trail.*
