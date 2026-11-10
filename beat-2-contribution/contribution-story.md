# Contribution: Show the size of the storage with `cluv status`

Path: `beat-2-contribution/contribution-story.md`

This file is Assignment 5, and it is graded at the path above; a copy kept anywhere else in
the repository is not read. **Keep every section header below under its exact name** — the
format criteria grade exactly that. Your course repo link is the whole submission: no tool
files and no evidence uploads are part of Assignment 5.

**Contribution Number:** 1
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/mila-iqia/cluv/issues/252
**Pull Request:** https://github.com/mila-iqia/cluv/pull/280

One repository, built across units 5 to 8, telling the whole story of your real
contribution. Fill it as you work, not at the end — the sections below ask for evidence that
is far easier to write down as it happens than to reconstruct in the last weekend.

Contribution Number stays `[1]` unless you make more than one contribution this term; it
just numbers them.

---

## Toolkit

Every story section below, from Why I Chose This Issue through Pull
Request, ends with a Toolkit block: which tool did that phase's work
and how you used it, what it produced, what difficulties or
shortcomings it presented, and what you changed or would change
because of them. Together the blocks are the thread the rubric's
Toolkit points read, and they are the raw material for your unit-9
portfolio, so keep them current as you work rather than
reconstructing them later.

A Toolkit block looks like this (copy it into each story section and
fill it there):

```text
**Toolkit**

- Tool: [which tool did this phase's work, and how you used it]
- What it produced: [described in a sentence or a short excerpt]
- Difficulties: [what fought you or fell short, or "none met"]
- What changed: [the patch, revision, or workaround, what you would
  update, or "nothing"]
```

This section also keeps one short dated entry per week of tool work,
starting below: what you built or changed that week, what broke, and
what you changed because of it. The blocks tell the story where it
happened; these entries are the toolkit's own timeline.

All four entries are graded. Keep every heading below, and fill each
one in the week it belongs to.

**Unit 5: Rebuilt my scout and graded my issue with it after picking it by hand**

My Unit 1 issue-select skill was missing from my Mac, so I rebuilt the scout from my course-repo
rubric, the Unit 1 evidence guide, and the Unit 5 starter, and added an AI/ML-in-Python fit
profile. I had already found and claimed cluv#252 by hand with `gh search issues`, so I graded it
directly instead of running a scan. The scout rejected it on one check, "Not claimed", because the
claim comment and PR #280 were my own. I patched that row to ignore claims and PRs by my own `gh`
account, logged the change in scan-notes.md, and the re-grade came out accept (6/6 checks pass).

**Unit 6: [one line on the tool work]**

[2-4 sentences: the voice-guard, the repo-memory file. What broke or
fell short, and what changed because of it. "Nothing broke" counts when
it is true - say how you know.]

**Unit 7: [one line on the tool work]**

[2-4 sentences: the ship-gate and your gate manifest. What it produced
on your real branch, what fought you, what changed because of it.]

**Unit 8: [one line on the tool work]**

[2-4 sentences: the PR-monitor and your playbook. What it read, what it
drafted, what you did with the draft.]

---

## Why I Chose This Issue

I wanted my first real contribution to be in AI and ML infrastructure, in Python. I started with
popular eval frameworks like inspect_ai, lighteval and lm-evaluation-harness. inspect_ai's
maintainers are very active, but every good-first-issue there was claimed within hours, and the
other two projects' issues were one to three years old. I then searched GitHub with
`gh search issues` for new, unassigned Python good-first-issues. Most results came from tiny
one-person repos, but one stood out: mila-iqia/cluv, the tool Mila (the Montreal AI research
institute) uses to run training jobs across GPU clusters. I checked its open issues one by one:
#278 and #256 already had PRs, and #193 needed real Slurm cluster access to test. #252 had no
comments, no assignee and no PR, and the maintainers had merged seven outside contributors' PRs
in the previous three weeks. It's small and clearly described, which made it a good first issue
to learn the full fork, test, PR flow on a real AI lab's code.

I searched by hand under time pressure and only graded the issue with my scout after I had
claimed it, so the scout didn't pick this issue for me. Its first verdict was reject: every check
passed except "Not claimed", which failed because the claim and the open PR were mine. That showed
a real gap in my rubric, which can't tell my own claim from a stranger's, so I patched the row and
the re-grade came out accept. The manual search also showed a gap I'd fix in the scout's strategy:
my first query put all my keywords in one quoted string, `gh` treated that as an exact phrase, and
it returned nothing. Per-repo queries for AI orgs I trust would have found cluv faster.

**Claim comment:** https://github.com/mila-iqia/cluv/issues/252#issuecomment-6750788609

**Toolkit**

- Tool: I found and claimed this issue by hand first, then graded it with my issue-scout
  (`claude "issue-scout: grade https://github.com/mila-iqia/cluv/issues/252"`). The search was by hand with
  `gh search issues --label "good first issue" --state open --no-assignee --language python
  --created ">=2026-10-19" --sort created --limit 100`, and used Claude Code to open the
  candidate threads on GitHub and check for claims, linked PRs, and recent maintainer merges.
- What it produced: 100 results, almost all from small one-person repos created that day.
  Among real AI/ML projects, inspect_ai's good-first-issues were all claimed (several with
  open PRs), and lighteval / lm-evaluation-harness issues were 1-3 years old. In mila-iqia/cluv,
  #278 already had a PR, #256 had an assignment request and a PR, #193 needed Slurm cluster
  access, and #252 had no comments, no assignee, and no PR. The scout's first grade on #252 was
  reject: 5 checks passed, but "Not claimed" failed on my own claim comment and PR #280.
- Difficulties: my first query passed `"llm OR eval OR ..."` as one quoted argument, so `gh`
  searched for that exact phrase and returned nothing. Popular AI repos get their
  good-first-issues claimed within hours.
- What changed: dropped the keyword phrase and filtered the results myself; next time I would
  write per-repo queries (`--repo` for AI orgs I trust). In the scout, I patched the "Not claimed"
  row to ignore claims and PRs by my own `gh` account and logged it in scan-notes.md; the
  re-grade came out accept.

---

## Understanding the Issue

**Problem Description**

`cluv status` shows how full your `$HOME` and `$SCRATCH` storage is on each cluster, but only
as a percentage bar. Every cluster has a different quota, so "30%" on its own doesn't tell you
whether you have 5 GiB or 500 GiB left. You have to log in and run a separate command to find out.

**Expected Behavior**

Next to the bar, the column should show how much is used out of the total, like
`148/500 GiB (30%)`, so you can compare clusters at a glance.

**Current Behavior**

It prints `▰▰▱▱▱▱▱▱▱▱   30%` and nothing else, even though the program already has both
numbers (148 and 500 GiB) when it draws the bar.

**Affected Components**

`_bar()` in `cluv/cli/status.py`, which formats each storage row; `_build_cluster_table()` in
the same file, which calls it; `StorageStats` in `cluv/slurm.py`, which holds the numbers; and
`tests/test_status.py` for the tests.

**Toolkit**

- Tool: Claude Code to read `cluv/cli/status.py`, `cluv/slurm.py`, and the repo's `CLAUDE.md`, and grep for every caller of `_bar()`.
- What it produced: `_bar()` is only called for the Storage column (`status.py` ~385/388), and the GiB values come from `StorageStats`, parsed in `slurm.py`.
- Difficulties: `CLAUDE.md` says `cluv status` uses mock data, which is out of date (real parsing exists in `slurm.py`); I checked the code instead of trusting the note.
- What changed: nothing

---

## Reproduction Process

**Environment Setup**

macOS on a MacBook Pro, CPython 3.13.0, `uv` (installed with Homebrew), and the GitHub CLI
`gh`. I forked and cloned with `gh repo fork mila-iqia/cluv --clone`, made the branch
`feat/252-storage-size` from `master` at commit `7a06e8b` (cluv `0.1.7.post1+7a06e8b`), and
ran `uv sync`, which created `.venv` and installed 90 packages. CONTRIBUTING.md says to skip
tests that need live clusters, so my baseline was
`uv run pytest -m "not integration and not slow" -q`: 337 passed, 3 failed, 12 xfailed.

Challenges:
- The 3 failures are `tests/test_hydra_plugin.py::test_hydra_launcher_is_discoverable[source-3.11/3.12/3.13]`,
  failing on unmodified `master` with `ModuleNotFoundError: No module named 'cluv.__main__'`.
  They don't touch `status.py`, so I recorded them as the baseline and checked that the same
  3 still fail (and nothing else) after my change.
- `pre-commit` isn't in the dev dependencies (`uv run pre-commit` failed with "Failed to
  spawn"). Their CI installs it with pip, so I ran `uvx 'pre-commit<4.0.0' run --all-files`.
- I have no access to Mila or DRAC clusters, so I can't run `cluv status` end to end. I
  reproduced at the function level by calling `_bar()`, the function that draws the column.

**Steps to Reproduce**

1. Clone mila-iqia/cluv at `7a06e8b` and run `uv sync`.
2. Run `uv run python -c "from cluv.cli.status import _bar; from cluv.utils import console; console.print('\$HOME    ', _bar(40, 75)); console.print('\$SCRATCH ', _bar(148, 500))"`
   (40 of 75 GiB used in `$HOME`, 148 of 500 GiB in `$SCRATCH`).
3. Observed: `$HOME ▰▰▰▰▰▱▱▱▱▱   53%` and `$SCRATCH ▰▰▱▱▱▱▱▱▱▱   30%`. Only the
   percentage is shown; the 40/75 and 148/500 GiB values passed in never appear.

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/himavanthkar/cluv/commit/0e7714dd262756fc1236e0168b27e8c33e78a20b (adds `test_bar_shows_used_and_total_size`, which fails on unmodified `master`)
- **Screenshots/logs:** before the fix, `_bar(40, 75)` / `_bar(148, 500)` printed
  `▰▰▰▰▰▱▱▱▱▱   53%` / `▰▰▱▱▱▱▱▱▱▱   30%`; the new test failed with
  `AssertionError: assert '148/500 GiB' in '▰▰▱▱▱▱▱▱▱▱   30%'` (1 failed, 1 passed).
- **My findings:** the sizes are already available. `StorageStats` carries
  `home_used/home_quota/scratch_used/scratch_quota` in GiB, and `_bar()` receives them but only
  uses them to compute the ratio. `_bar()` is only called for the Storage column and had no
  unit test.

**Toolkit**

- Tool: Terminal with `uv` and `pytest`, and Claude Code to find which function draws the Storage column and write the one-line repro.
- What it produced: the before output above, and a failing test committed on its own (0e7714d).
- Difficulties: no cluster access, so the repro is at the function level, not a real `cluv status` run; 3 unrelated hydra tests fail locally.
- What changed: recorded the 3 hydra failures as a baseline instead of trying to fix them, since they're outside this issue.

---

## Solution Approach

**Analysis**

`cluv status` builds the Storage column in `_build_cluster_table()` (`cluv/cli/status.py`,
~line 385), which calls `_bar(c.storage.home_used, c.storage.home_quota)` and the same for
`$SCRATCH`. `_bar()` (~line 295) computes `ratio = used / total` and returns only
`f"{bar_str} {pct:4.0f}%"`, so the used and total values it is given are never printed.

**Proposed Solution**

Change only the text `_bar()` returns: print `used/total GiB (pct%)` after the bar. When the
quota is unknown (`total == 0`), keep the old output so it doesn't show a misleading `0/0 GiB`.
Not changing: how sizes are parsed in `slurm.py`, the GPU bars (`_gpu_bar`), the jobs table,
or the unit (GiB only; TiB for big quotas is an open question for the maintainer).

**Implementation Plan**

1. Add `test_bar_shows_used_and_total_size` and `test_bar_without_quota_shows_no_size` to
   `tests/test_status.py` first, and commit them on their own (0e7714d): the size test fails.
2. Modify `_bar()` in `cluv/cli/status.py` to return `used/total GiB (pct%)`, keeping the old
   output when `total == 0` (7fc94ea).
3. Run the status tests, the full non-integration suite, and pre-commit; open the PR.

Deviations: none. The diff matches the plan; the TiB question is asked in the PR, not
implemented.

**Toolkit**

- Tool: Claude Code to draft the plan and the patch, which I applied and ran myself.
- What it produced: a 5-line change to `_bar()` plus 2 tests; both new lines fit the repo's 99-character ruff limit.
- Difficulties: none met
- What changed: nothing

---

## Testing Strategy

- Repro re-run. Before: `$SCRATCH ▰▰▱▱▱▱▱▱▱▱   30%`. After:
  `$SCRATCH ▰▰▱▱▱▱▱▱▱▱ 148/500 GiB (30%)` (and `$HOME ... 40/75 GiB (53%)`).
- New tests, failing first: before the fix, `uv run pytest tests/test_status.py -m "not slow"`
  gave 1 failed, 1 passed (`AssertionError: assert '148/500 GiB' in '▰▰▱▱▱▱▱▱▱▱   30%'`).
  After: 2 passed.
- Full suite: `uv run pytest -m "not integration and not slow" -q` went from 337 passed to
  339 passed; the same 3 pre-existing `test_hydra_plugin` failures, nothing new.
- Lint/format: `uvx 'pre-commit<4.0.0' run --all-files`, all 13 hooks Passed (incl. ruff
  check, ruff format, codespell).
- Not tested: a real `cluv status` against live cluster quotas (no cluster access); said so in
  the PR.

**Toolkit**

- Tool: `pytest` through `uv run`, `pre-commit` through `uvx`, and the one-line repro command.
- What it produced: fail-then-pass for the new test, 337 → 339 passed overall, all pre-commit hooks passing.
- Difficulties: `uv run pre-commit` failed because pre-commit isn't a project dependency.
- What changed: ran it with `uvx 'pre-commit<4.0.0'`, matching their CI.

---

## Implementation Notes

[A running log, added to as you work. Date each entry. What you
built, decisions you made when you made them, wrong turns included.]

**2026-11-09**

Picked mila-iqia/cluv#252 (unclaimed good-first-issue in Mila's ML cluster CLI) after checking
inspect_ai, lighteval, lm-evaluation-harness, ragas and deepeval, where the open good-first-issues
were either already claimed or stale. Instructor OK'd an issue from outside the course site.
Posted my claim comment. Forked and cloned, `uv sync` on macOS with CPython 3.13.0 at commit
`7a06e8b`. Baseline `uv run pytest -m "not integration and not slow" -q`: 337 passed,
3 failed, 12 xfailed; the 3 failures are `tests/test_hydra_plugin.py::test_hydra_launcher_is_discoverable[source-3.11/3.12/3.13]`,
which don't touch `status.py`. Reproduced the issue by calling `_bar()` directly:
`_bar(148, 500)` prints `▰▰▱▱▱▱▱▱▱▱   30%` with no used/total size.

Wrote the test first and pushed it on its own (https://github.com/himavanthkar/cluv/commit/0e7714dd262756fc1236e0168b27e8c33e78a20b): the size test failed, the 0-quota test
passed. Then changed only `_bar()` in `cluv/cli/status.py` to print `used/total GiB (pct%)`, keeping
the old output when the quota is 0. After: `148/500 GiB (30%)`, both new tests pass, full run
339 passed / same 3 hydra failures, pre-commit all passed. `pre-commit` isn't in the dev
dependencies, so I ran it with `uvx 'pre-commit<4.0.0'` like their CI does. Fix commit: https://github.com/himavanthkar/cluv/commit/7fc94ea0e392531f8918759064b48e90c06ccd26.
Opened https://github.com/mila-iqia/cluv/pull/280 under my own account.

**Toolkit**

- Tool: git and `gh` in Terminal; Claude Code to keep the log of commands and outputs.
- What it produced: the dated entry above, with commit links.
- Difficulties: a `git diff` opened a pager and looked like the Terminal had frozen (pressed `q`).
- What changed: nothing

---

## Pull Request

**PR Link:** https://github.com/mila-iqia/cluv/pull/280

**PR Description:** The PR follows the repo's template (Summary, Issues). It explains that the
Storage column only showed a percentage, shows before/after output, lists the tests (the
failing-first commit, 339 passed with the 3 pre-existing hydra failures named, pre-commit
passing), says I couldn't test against a real cluster, asks whether TiB or a `vulcan`-style
layout is preferred for large quotas, discloses that I used Claude Code, and ends with
`Closes #252`.

**Maintainer Feedback:**

- 2026-11-09: automated Copilot review recommended approval, no issues found. No maintainer feedback yet.

**Status:** Awaiting review

**Toolkit**

- Tool: `gh pr create --repo mila-iqia/cluv --base master --head himavanthkar:feat/252-storage-size --body-file pr_body.md`, so the PR is opened under my own account.
- What it produced: https://github.com/mila-iqia/cluv/pull/280; GitHub's automated Copilot review recommended approval with no issues.
- Difficulties: the repo needs one human approval to merge, and CI may wait for a maintainer to approve running it for a first-time contributor.
- What changed: nothing yet; waiting on maintainer review.

---

## Learnings and Reflections

**Technical Skills Gained**

Setting up a Python project with `uv` (`uv sync`, `uv run`, `uvx`). Writing a test that fails
first to prove a bug, then making it pass. Reading `rich` text output in tests with `.plain`.
Running a repo's own lint and format checks (pre-commit, ruff, codespell) the same way its CI
does. Forking a repo and opening a PR from the command line with `gh`.

**Challenges Overcome**

- Finding an unclaimed issue: the popular AI repos were claimed within hours, so I had to check
  threads one by one.
- 3 tests failed before I changed anything. I recorded them as a baseline and showed the same 3
  still failed after, instead of trying to fix unrelated code.
- `pre-commit` wasn't installed in the project, so I matched their CI with `uvx`.
- I had no cluster access, so I reproduced the bug by calling the formatting function directly.

**What I'd Do Differently Next Time**

Start looking for an issue much earlier, and claim it the moment I decide, before someone else
does. Run my scout first instead of searching by hand. Keep this story updated each day instead
of filling it in at the end.

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- cluv's CONTRIBUTING.md (setup with `uv sync`, skipping `integration` tests): https://github.com/mila-iqia/cluv/blob/master/CONTRIBUTING.md
- cluv's CI workflow, which showed how they install and run pre-commit: https://github.com/mila-iqia/cluv/blob/master/.github/workflows/build.yaml
- The issue thread itself: https://github.com/mila-iqia/cluv/issues/252
- `gh search issues` manual, for the search flags: https://cli.github.com/manual/gh_search_issues
