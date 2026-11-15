# Contribution: server: verify external OIDC tokens for self-hosted deployments

**Contribution Number:** 3
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/mozilla-ai/cq/issues/393
**Pull Request:** not opened yet (design must be agreed with the maintainers first)

<!--
Extra contribution, not graded. Sections are filled only as far as the work has gone.
Sections marked REWRITE are drafts for me to put in my own words.
-->

---

## Toolkit

### 2026-11-14 to 2026-11-15: shortlist, claim, setup, design notes

I ran my `oss-contribute` skill, Phase 0 to Phase 3. The scout graded six issues and accepted
this one with the note "ambitious". repro-check accepted the claim draft as a claim-only package
and the voice guard passed it. Nothing broke in the tools this round. One friction line went into
the scout's `scan-notes.md`: its "Not claimed" check cannot express a partly claimed issue
(Nimbus#973), which is a different issue from this one.

---

## Why I Chose This Issue

> REWRITE: this section is a draft for me to put in my own words.

I started from `ai_ml_issues.csv` (780 rows from the AI301 Issue Selection site). After dropping
discussion/umbrella issues, issues needing hardware I don't have, and repos with no Python or
TypeScript, 552 remained; 169 Python/TypeScript issues were still open with no assignee and no
open linked PR. I ran the full Phase 1 checks on 24 of them. Most were already claimed in the
comments or already fixed; 9 survived.

The scout accepted four of the six it graded: llmsectest#36, cq#393, scikit-learn#18186 and
Daft#4927. I picked cq#393: a Mozilla AI project, Python and FastAPI, a clearly scoped feature
with its out-of-scope items listed, and labels `good first issue` and `help wanted`. The cost is
that the issue asks for the design to be agreed in a maintainer-opened RFC before any code, which
my fit profile normally avoids.

Health checks on 2026-11-14: last commit on `main` 2026-11-05; days to first human response on
five recent merged outside PRs were 0.3, 0.7, 8.7, 10.1 and 14.2; CONTRIBUTING.md promises a
first response within 5 business days; no AI policy is stated in the repo.

**Toolkit**
- Tool: issue-scout in direct mode on six URLs, after `gh` health checks from `oss-contribute` Phase 1.
- What it produced: verdict **accept** for cq#393, all required checks passing (recent commits,
  maintainer responsive, scope feasible, not claimed, AI policy), difficulty note "ambitious"
  because of the design-first step.
- Difficulties: none met on this issue. On Nimbus#973 the "Not claimed" check had no way to
  record that one of three files was claimed and two were free.
- What changed: one dated line appended to `scan-notes.md` about partial claims.

---

## Understanding the Issue

> REWRITE: this section is a draft for me to put in my own words.

### Problem Description

A self-hosted `cq` server cannot verify an access token issued by an external identity provider
(Keycloak, Okta, Entra ID). The CLI side of OIDC has landed; the server side has not.

### Expected Behavior

With optional `CQ_OIDC_ISSUER`, `CQ_OIDC_AUDIENCE` and `CQ_OIDC_JWKS_URL` set, the server
validates `iss`, `aud`, `exp` and an RS256/ES256 signature against the issuer's cached JWKS, and
maps a configurable claim to the identity recorded as `created_by`. With the config absent,
behavior is unchanged.

### Current Behavior

The server authenticates two ways only: a JWT it signed itself with `CQ_JWT_SECRET` (HS256), and
a long-lived API key compared with HMAC.

### Affected Components

- `server/backend/src/cq_server/auth.py` (`verify_token`, HS256 only)
- `server/backend/src/cq_server/api/deps.py` (`get_current_user`, `require_api_key`)
- `server/backend/src/cq_server/core/config.py` (`Settings`)
- `server/backend/pyproject.toml` and `uv.lock` (`pyjwt` without the `crypto` extra)

**Toolkit**
- Tool: `gh issue view` for the thread, `grep` and reading on a fresh clone of `main` at 2e538ec.
- What it produced: the call sites above, each checked against the code before it went into the claim.
- Difficulties: none met.
- What changed: nothing.

---

## Reproduction Process

### Environment Setup

- Fork: https://github.com/himavanthkar/cq (`origin`); mozilla-ai/cq is `upstream`.
- Branch: `feature/393-oidc-token-verification` from `upstream/main` at 2e538ec. The repo asks
  for the `feature/` prefix.
- Install: `make setup-server-backend` (uv sync with the dev group and the semsearch extra).
- Baseline, 2026-11-14, macOS, Python 3.14.3, pyjwt 2.15.0: `uv run pytest -q` in
  `server/backend` gave **357 passed, 18 skipped, 97 warnings**. The skip reasons shown by
  `-rs` were PostgreSQL unavailable (no Docker running and no `CQ_TEST_DATABASE_URL`) and
  missing semantic-search dependencies.
- Lint, re-run 2026-11-15: `make lint-server-backend` exited 0. Hooks passed: merge conflicts,
  trailing whitespace, end of files, ruff, ruff format, detect-secrets, ty (server/backend),
  uv lock --check (server/backend). Hooks for other components were skipped (no files).

### Steps to Reproduce

This is a feature request, so there is no bug to reproduce. What I confirmed in the code:

1. `verify_token` calls `jwt.decode(token, secret, algorithms=["HS256"])`; its only caller is
   `get_current_user`.
2. `import cryptography` fails in the synced environment, and `uv.lock` has no `cryptography`
   entry, so PyJWT cannot verify RS256/ES256 as installed.

### Reproduction Evidence

- **Commit showing reproduction:** none; no code is written yet.
- **My findings:** see Implementation Notes, 2026-11-14.

**Toolkit**
- Tool: repro-check (claim-only mode) on `claim-draft.md`, then the voice guard.
- What it produced: repro-check **accept**; the two claim-side checks passed (AI disclosure: the
  repo states no policy; claim respects conventions) and the four repro checks were reported as
  not yet applicable. Voice guard: `voice-guard: pass. Your rules hold; you can post it.`
- Difficulties: none met.
- What changed: nothing.

**Claim:** https://github.com/mozilla-ai/cq/issues/393#issuecomment-6887225214
(posted 2026-11-15T01:21:05Z; the thread had no assignee, no other claim and no linked PR right
before posting).

---

## Solution Approach

Not started. The maintainers open the RFC; I have a local `design-notes.md` draft only.

---

## Testing Strategy

Not started. Draft idea in the design notes: generate an RSA key pair in the test, sign tokens
with it and stub the JWKS fetch, so no network or real IdP is needed.

---

## Implementation Notes

### 2026-11-14

Design findings from reading the code (no feature code written):

- **User provisioning is the main open question.** `users.password_hash` is NOT NULL and
  `api_keys.user_id` references `users.id`. A person signing in through an external IdP has no
  `users` row, so `GET /me` would return 404 and they could not own an API key.
- **`created_by` is set on the API-key path.** The issue wants the mapped claim in `created_by`,
  but `propose_unit` takes its username from `require_api_key`, not from the JWT path. I asked
  about this in the claim.
- **PyJWT already covers the JWKS part.** `PyJWKClient` in 2.15.0 caches the key set and
  refetches once on an unknown `kid`, limited by a cooldown.
- **A new dependency is needed.** `pyjwt[crypto]` would add `cryptography`; CONTRIBUTING asks
  for new dependencies to be discussed first.
- **Server-issued tokens carry no `iss` claim** (`create_token` sets `sub`, `iat`, `exp`), so
  routing on `iss` would leave today's tokens unchanged.

Not verified: nothing run against a real IdP; the CLI's Go sign-in code not read.

### 2026-11-15

Claim posted. Waiting for a maintainer reply before any code.

---

## Pull Request

**PR Link:** none yet

**Maintainer Feedback:**

- 2026-11-15: none yet.

**Status:** Claimed; waiting on maintainers to start the RFC.

---

## Learnings and Reflections

> REWRITE: for me to write in my own words once there is something to reflect on.

---

## Resources Used

- Issue: https://github.com/mozilla-ai/cq/issues/393
- cq CONTRIBUTING.md and DEVELOPMENT.md
- PyJWT 2.15.0 `PyJWKClient` source, read from the installed package
