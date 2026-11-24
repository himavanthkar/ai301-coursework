# Contribution: Give a first run a target whose clean result can be checked

**Contribution Number:** 6
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/wehnsdaefflae/llmsectest/issues/15
**Pull Request:** not opened yet (claimed 2026-11-24)

<!--
Extra contribution, not graded. Sections are filled only as far as the work has gone.
Sections marked REWRITE are drafts for me to put in my own words.
-->

---

## Why I Chose This Issue

> REWRITE: this section is a draft for me to put in my own words.

Same project as my PR #48, with an owner who answered my last claim in four days and merges
outside PRs within days. The issue asks for a `--target testbed` shortcut (or a documented
recipe) pointing at a target with known ground truth, a test, and a docs note.

**Toolkit**
- issue-scout, separate `claude -p` run, 2026-11-24: **accept**, difficulty "ambitious" (flag or
  recipe is an open design choice; the test must not make a network call). All five required
  checks pass; the good-first-issue label check is info only now.
- Gaps the scout named in its own evidence: it could not list the last five commits or read
  the issue timeline because `gh api` was not allowed in that run.

---

## Claim

Posted 2026-11-24:
https://github.com/wehnsdaefflae/llmsectest/issues/15#issuecomment-7138594230

What I found before claiming: `--target` already exists and an app target is any value starting
with `app:`; the guide names `mloda-rag-testbed`, whose README starts it locally on port 8000
with `POST /chat` taking `message`, `user` and `conversation_id`; I found no hosted instance.
One question, with my default: assume `http://localhost:8000/chat` and add the flag, unless the
owner prefers a documented recipe with no flag.

**Toolkit**
- repro-check, separate `claude -p` run: **accept** (claim-only). It verified the code and docs
  claims against the clone. It could not fetch the testbed README, so those details rest on my
  own read of it through `gh api`. It also noted I had not said I read
  `src/llmsectest/adapters/app_endpoint.py`, which the issue lists first.
- Voice guard: pass.

---

## Status

2026-11-24: claimed, waiting for the owner's answer. No branch yet.
