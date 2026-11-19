# Contribution: `cluv status` should detect unavailable clusters

**Contribution Number:** 5
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/mila-iqia/cluv/issues/297
**Pull Request:** not opened yet (claimed 2026-11-19)

<!--
Extra contribution, not graded. Sections are filled only as far as the work has gone.
Sections marked REWRITE are drafts for me to put in my own words.
-->

---

## Toolkit

### 2026-11-19: scout, claim

I ran my `oss-contribute` skill, Phase 1 and Phase 2. Every tool check below was a separate
`claude -p` run or a real run of `check_voice.py`.

---

## Why I Chose This Issue

> REWRITE: this section is a draft for me to put in my own words.

Same project as my merged PR #289 and my open PR #280, so the maintainers see my name again. The
issue was opened on 2026-11-18 by hvdbm, the maintainer who merged #289. It asks for a timeout
when `cluv status` reads cluster info, with a warning, and suggests a live table for
`cluv status clusters` like the one in `cluv submit`. I decided on 2026-11-19 to stop limiting
myself to good-first-issue work, so I claimed both parts.

**Toolkit**
- Tool: issue-scout, direct mode. Separate `claude -p` run on 2026-11-19.
- What it produced: verdict **accept**, difficulty "ambitious" (the live table is an open design
  edge). Recent commits: pass. Maintainer responsive: pass. scope-feasible: pass. Not claimed:
  pass (no assignees, 0 comments, no linked PR). AI-friendly policy: pass (nothing stated; the
  repo ships AGENTS.md and CLAUDE.md). Good first issue label: fail (only `enhancement`).
- Difficulties: the first `claude -p` run graded nothing. It was denied reading the rubric in
  `~/.claude/skills/issue-scout/` and running `gh issue view`. I re-ran it with read-only
  `--allowedTools` and `--add-dir` for the skill folder.
- What changed: the "Good first issue label" check went from preferred to info only (it is
  reported but never ranks), and it now accepts both spellings of the label. The label filter is
  gone from the search in `oss-contribute`, and my fit profile now says small, moderate and hard
  issues.

---

## Claim

Claim comment, posted 2026-11-19:
https://github.com/mila-iqia/cluv/issues/297#issuecomment-7005038089

What it says: `status()` fetches every cluster with one `asyncio.gather` over
`get_cluster_status()` and the `remote.get_output(...)` call has no timeout, so one silent cluster
holds back the whole table; the jobs table is fetched the same way. First step: a test that fakes
a remote which never returns. One question, with my default: timeout first and the live table as
a second PR, unless the maintainer prefers one PR.

**Toolkit**
- Tool: repro-check on the claim draft, two separate `claude -p` runs on 2026-11-19; voice guard
  (`check_voice.py --draft`) on each wording.
- What it produced: run 1 (timeout-only wording): **accept**, and it checked the four
  `status.py` claims against the code (lines 571, 231, 235, 538). Run 2 (final wording, with the
  live table): **accept** on both claim checks, but it was denied `git show`, so it did not
  re-verify the code claims. Voice guard: pass both times.
- Difficulties: the one code claim new in the final wording (that `cluv/cli/submit.py` uses rich
  `Live`) was checked by hand with `git grep` on upstream master (lines 21 and 246), not by the
  tool.
- What changed: nothing in the tool. Run 1 noted that the connection check before the SSH command
  also has no timeout, so the timeout has to wrap each whole fetch. That goes into the plan.

---

## Reproduction

2026-11-19, branch `feat/297-status-timeout` from upstream master `8c52086`.

Baseline before any change (`uv run pytest -m "not integration" -q`): 5 failed, 348 passed,
86 skipped, 12 xfailed. The 5 failures are on the unmodified base: 3 hydra tests (the
environment-dependent ones from #257) and 2 `test_order_of_flags_in_sbatch_args_from_cli_is_preserved`
cases I have not investigated.

I have no cluster access, so I reproduced at the function level: a fake remote whose `get_output`
never returns, passed to `get_cluster_status("rorqual", ...)`, with a 10 s outer limit:

```
still waiting after 10.0s: get_cluster_status never returned; gave up
```

Failing tests, committed alone:
[e75425a](https://github.com/himavanthkar/cluv/commit/e75425a). One for `get_cluster_status`, one
for `fetch_live_job_info`. On the unfixed code:

```
E                   TimeoutError
FAILED tests/test_status.py::test_get_cluster_status_times_out_on_unresponsive_cluster
FAILED tests/test_status.py::test_fetch_live_job_info_times_out_on_unresponsive_cluster
2 failed in 6.60s
```

Choices the plan may change: the constant name `CLUSTER_TIMEOUT_SECONDS` and the exact warning
text (taken from the issue). Lint (`uvx 'pre-commit<4.0.0' run --files tests/test_status.py`): all
hooks passed.

---

## Status

2026-11-19: claimed; reproduction and failing tests pushed to my fork. Waiting for the
maintainer's answer on one PR or two. Next: `plan.md` and plan-check.
