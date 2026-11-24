# Contribution: Stack Overflow connector: index answers (`stackoverflow:answer`)

**Contribution Number:** 8
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/nimbus-agent/Nimbus/issues/951
**Pull Request:** not opened yet (claimed 2026-11-24, blocked, see Status)

<!--
Extra contribution, not graded. Sections marked REWRITE are drafts for me to put in my own words.
-->

---

## Why I Chose This Issue

> REWRITE: this section is a draft for me to put in my own words.

A TypeScript issue from my 2026-11-14 shortlist that was still unclaimed. The maintainer wrote
out where the code lives, what the codebase cares about, and an acceptance checklist, and the
work can be proven with a local fake server.

**Toolkit**
- issue-scout, separate `claude -p` run, 2026-11-24: **accept**, difficulty "ambitious" (request
  cost against a 60-per-minute limit, the embedding choice for private answers, and an id scheme
  are all left to the contributor). It could not read `docs/CONTRIBUTING.md` in that run; I read
  it afterwards with `gh api`.

---

## Claim

Posted 2026-11-24:
https://github.com/nimbus-agent/Nimbus/issues/951#issuecomment-7140013979

CONTRIBUTING says to comment and be assigned before starting. My claim says what I read
(`stackoverflow-sync.ts`, `stackoverflow-question-mapping.ts`), my first step (confirm the
answers endpoint in the Teams API docs), my plan for the id scheme, the request bound and
failure handling, and one question with my default: `LOCAL_ONLY_PROSE_TYPES` for embedding.

**Toolkit**
- Voice guard run 1: **STOP**. My draft opened with "Could you assign it to me?", which breaks
  my own rule against asking to have an issue reserved. I reworded it to say I was commenting
  before starting, as CONTRIBUTING asks. Run 2: pass.
- repro-check, separate `claude -p` run: **accept** (claim-only). It verified the code claims
  against the two source files and could not read CONTRIBUTING itself.

---

## Status

2026-11-24: claimed, fork cloned, not assigned yet. No code yet.

- Bun: I installed Bun 1.4.5 with Homebrew (CI uses 1.4). `bun install --frozen-lockfile`
  installed 1061 packages, and the two existing Stack Overflow test files give `28 pass, 0 fail`
  on `main` at `5b1d570e2`.
- Answers endpoint: the live Swagger spec needs a login, so I read the official SDK repo
  (StackExchange/StackOverflowSDK, generated from its `swagger.json`, last pushed 2025-11-10).
  It lists answers per question only: `GET /teams/{team}/questions/{questionId}/answers`, with
  `page` and `pageSize` (15, 30, 50 or 100) and the same envelope as questions. List items have
  `body`, `isAccepted` and `questionId` but not `bodyMarkdown`. It says nothing about whether
  question and answer ids can be equal, and nothing about rate limits. This is not checked
  against the live API.
