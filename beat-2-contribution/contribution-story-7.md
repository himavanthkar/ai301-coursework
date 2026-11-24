# Contribution: dataset push reports "already pushed ... Skipping" while files are missing

**Contribution Number:** 7
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/mila-iqia/cluv/issues/260
**Pull Request:** https://github.com/mila-iqia/cluv/pull/304

<!--
Extra contribution, not graded. Sections marked REWRITE are drafts for me to put in my own words.
-->

---

## Why I Chose This Issue

> REWRITE: this section is a draft for me to put in my own words.

A bug filed by hvdbm, unclaimed, with no open PR, in the project where I already have a merged
PR. It can be reproduced without a cluster: the skip decision is local, and rsync can be mocked.
I looked at #236 too and left it: it needs a design decision first (a new flag, or docs only).

---

## Claim, reproduction, fix (all 2026-11-24)

Claim: https://github.com/mila-iqia/cluv/issues/260#issuecomment-7140006698 . One question with
my default: keep the skip and look at the whole tree, unless they prefer to always run rsync.

Cause: `_push_datasets_to_remote()` in `cluv/cli/sync.py` compared the last push time with
`local_source.stat().st_mtime`, the top directory only.

Failing test, committed alone ([5d43715](https://github.com/himavanthkar/cluv/commit/5d43715)):
push once, add `train/b.txt`, push again, with rsync mocked.

```
E       AssertionError: assert 1 == 2
1 failed, 8 passed in 1.25s
```

Fix ([ca54e28](https://github.com/himavanthkar/cluv/commit/ca54e28)): `_latest_edit_time()`
walks the source, follows symlinks with a visited set, uses `lstat` too, and skips the folders
rsync excludes. After: `12 passed`; full suite `5 failed, 397 passed` against a baseline of
`5 failed, 392 passed` (the same 5, all caused by my machine).

**Toolkit**
- repro-check on the claim, separate `claude -p` run: **accept**. It noted that `lstat` alone
  would not see a change in a file that a link points to.
- plan-check run 1: **reject** on "Honest unknowns". `os.walk` does not follow links to
  directories by default, so a change inside a linked directory would still be missed while
  rsync (`--copy-links`) would copy it. I changed the helper to follow links without looping
  and added a test with a link that points back up the tree.
- plan-check run 2: **accept**, all six checks.
- ship-gate: **PASS** on the first run, pr-precheck all five checks. Voice guard: pass.
- What changed in my tools: the working files for each cluv issue now live in their own
  `notes-<issue>/` folder, because the clone root only holds one plan at a time.

Limits I stated in the PR: no real push to a cluster; the walk's cost on a large dataset is not
measured; a file edited in place with its mtime reset would still be missed.

---

## Status

2026-11-24: PR #304 open, waiting for review.
