# Contribution: `cluv submit first` exits 0 when every submission fails

**Contribution Number:** 2
**Student:** Himavanth Karpurapu
**Issue:** https://github.com/mila-iqia/cluv/issues/257
**Pull Request:** https://github.com/mila-iqia/cluv/pull/289

---

## Toolkit

This round I used the whole toolkit in order, before posting anything: scout, repro-check,
voice guard, plan-check, ship-gate, and the PR monitor.

### Unit 5: Graded a second cluv issue with the patched scout

I graded #257 directly with my issue-scout (after the Unit 5 and 6 patches): accept, difficulty
"straightforward", with one preferred check failing because the issue is labeled only `bug`. The
scout also warned that open PR #232 edits the same racing code and that recent outside PRs in cluv
had no maintainer review yet. I logged that second point in scan-notes.md as a possible patch: my
"Maintainer responsive" check passes on merges alone and doesn't look at reviews of outside PRs.

### Unit 6: The voice guard checked a real claim before it went out

This time the claim went through repro-check and the voice guard before posting, instead of being
checked afterwards. Repro-check accepted it but caught a wrong sentence I had written ("other failed
`cluv submit` runs would exit 1"); `submit()` has only one `return None`, so I removed it. Claude
Code's auto mode blocked posting the comment as an external write, so I ran
`check_voice.py --draft claim-draft.md` by hand (pass) and posted it from Terminal. I also added the
repo's new `@AGENTS.md` import to my `CLAUDE.local.md`.

### Unit 7: The ship-gate stopped a PR that looked like 20 files

My first ship-gate run stopped: pr-precheck failed Diff matches plan, Description matches diff, and
Clean diff because the diff showed 20 files of other people's work. My local `master` was still at
last week's commit even though my branch started from the updated `origin/master`. After
`git branch -f master origin/master` the diff was my 3 files and the gate passed all checks. Before
that, plan-check rejected my first plan on Honest unknowns: it stated the CLI-level exit as settled
while my claim's question to the maintainer was unanswered, and it asserted a test baseline that
didn't match its own run.

### Unit 8: The monitor read #289 and found nothing to answer

`monitor.py --state` read PR #289 and again labeled it FEEDBACK-IN because of Copilot's automated
review. My playbook rule that bot comments need no reply covers it, so I posted nothing and will
check again after my 12-day threshold.

---

## Why I Chose This Issue

<!-- Rewrite in your own words. -->

After my first PR (#280) in mila-iqia/cluv, I followed the course advice to stay with the same
project, so the maintainers see the same name more than once. I checked cluv's open issues: #254
was already claimed with a PR open, #256 and #278 had PRs, and #259 and #193 need real cluster
access. #257 was opened by the maintainer hvdbm, had no comments, no assignee and no PR, and is a
real bug: a command that fails but reports success. My scout graded it accept and "straightforward"
(the change is local to `cluv/__main__.py`). I want to learn how a CLI turns a function's result
into an exit code, and to get a second PR into the same codebase.

**Claim comment:** https://github.com/mila-iqia/cluv/issues/257#issuecomment-6848675014

**Toolkit**
- Tool: issue-scout, `claude "issue-scout: grade https://github.com/mila-iqia/cluv/issues/257"`.
- What it produced: accept, difficulty straightforward, all required checks passing; the preferred
  good-first-issue check failed (label is `bug` only). It flagged open PR #232 as touching the same
  racing code and noted that sampled outside PRs had no maintainer reviews.
- Difficulties: "Maintainer responsive" passes on recent merges alone, even when outside PRs are
  waiting without review.
- What changed: logged a possible patch in scan-notes.md: count reviews of outside PRs, not just
  merges.

---

## Understanding the Issue

<!-- Rewrite in your own words. -->

### Problem Description

`cluv submit first` sends the job to several clusters and keeps the first one that starts. When
every one of those submissions fails, the command still exits with status 0, so scripts and CI think
it worked.

### Expected Behavior

When every submission fails, `cluv submit` should exit with a non-zero status (I chose 1).

### Current Behavior

It logs "All job submissions have failed! Exiting." and exits 0.

### Affected Components

`main()` in `cluv/__main__.py`, which runs the subcommand and ignores what it returns; `submit()` in
`cluv/cli/submit.py`, which returns `None` on that path by design; the Hydra launcher in
`hydra_plugins/cluv/cluv_launcher.py`, which already relies on that `None`; and `docs/commands.md`.

**Toolkit**
- Tool: Claude Code to read `submit.py`, `__main__.py`, the Hydra launcher, and `AGENTS.md`.
- What it produced: the cause is one level above where the issue points: `submit()` returning
  `None` is its documented contract, and `main()` drops it.
- Difficulties: none met.
- What changed: I planned the fix in `main()` instead of `submit()`, so the Hydra launcher keeps
  working.

---

## Reproduction Process

### Environment Setup

macOS, CPython 3.13.0, `uv`. Synced my fork with `gh repo sync himavanthkar/cluv`, then
`git switch -c fix/257-submit-exit-code origin/master` (master @ `15f430d`) and `uv sync`. The 3
`tests/test_hydra_plugin.py::test_hydra_launcher_is_discoverable[source-*]` tests turned out to
depend on the environment: from one Terminal session the baseline was 351 passed, 9 xfailed,
3 xpassed; in another session (and inside Claude Code) those 3 failed with
`ModuleNotFoundError: No module named 'cluv.__main__'`.

### Steps to Reproduce

1. Check out mila-iqia/cluv at `15f430d` and run `uv sync`.
2. Fake `submit` returning `None` (what it returns when every racing job fails) and run the real
   CLI entry point:
   `uv run python -c "from unittest import mock; import cluv.__main__ as m; m.submit = mock.AsyncMock(spec=m.submit, return_value=None); m.main(['submit', 'first', '--', 'python', 'main.py'])"; echo "exit code: $?"`
3. Observed: `exit code: 0`.

### Reproduction Evidence

- **Commit showing reproduction:** https://github.com/himavanthkar/cluv/commit/59fccd9 (adds
  `test_submit_exits_nonzero_when_every_submission_fails`, which fails with
  `Failed: DID NOT RAISE SystemExit`)
- **Screenshots/logs:** `exit code: 0` from the command above; `1 failed in 1.25s` for the new test.
- **My findings:** `submit()` has only one `return None`, the all-failed race, so the fix can't change
  any other submit path. I can't run a real all-failed race without cluster access, so the repro
  fakes `submit`.

**Toolkit**
- Tool: repro-check on my claim draft (`claude "repro-check: grade my draft claim comment in claim-draft.md for issue ..."`),
  then the one-line repro and pytest in Terminal.
- What it produced: accept on the claim, plus a correction: my sentence "other failed `cluv submit`
  runs would exit 1" was wrong.
- Difficulties: auto mode blocked Claude Code from posting the claim.
- What changed: removed the wrong sentence after checking the code myself; ran the voice guard by
  hand and posted from Terminal.

---

## Solution Approach

### Analysis

When every racing job fails, `submit()` (`cluv/cli/submit.py`, ~line 256) logs "All job submissions
have failed! Exiting." and returns `None`; its docstring says "Returns None if the submission
failed." `main()` (`cluv/__main__.py`, ~line 162) runs it with `asyncio.run(function(**args_dict))`
and ignores the result, so the process exits 0.

### Proposed Solution

Keep `submit()` unchanged and, in `main()`, call `sys.exit(1)` only when the subcommand is `submit`
and it returned `None`. Not changing: `submit()` itself, the racing logic that open PR #232 edits,
other subcommands (most return `None` normally), or the Hydra launcher.

### Implementation Plan

1. Add `test_submit_exits_nonzero_when_every_submission_fails` to `tests/test_submit.py` and commit
   it on its own (59fccd9).
2. In `cluv/__main__.py`, keep the subcommand's result and `sys.exit(1)` when `subcommand == "submit"`
   and the result is `None` (8d20fcb).
3. Per AGENTS.md, note the exit status under the `cluster` argument of `cluv submit` in
   `docs/commands.md` (9c4ee63).
4. Run the repro, the new test, the non-integration suite, and pre-commit.

Deviations: none. The question of CLI-level exit vs. `submit()` raising is still open with the
maintainer and is asked in the PR.

**Toolkit**
- Tool: plan-check, `claude "plan-check: grade my plan in plan.md and draft comment in plan-comment.md for issue ..."`.
- What it produced: reject on the first plan (Honest unknowns), then accept on the revised plan.
- Difficulties: the first plan presented the CLI-level exit as decided although my claim had asked
  the maintainer, and it stated a baseline that didn't match plan-check's own test run.
- What changed: added a Risks and unknowns section (open question, my choice of exit code 1,
  mocked-only coverage, environment-dependent hydra tests) and replaced the single baseline with
  both observed results.

---

## Testing Strategy

- Repro re-run: `exit code: 0` before, `exit code: 1` after.
- New test, failing first: `DID NOT RAISE SystemExit` at 59fccd9, `1 passed` after the fix.
- Full suite: `uv run pytest -m "not integration and not slow" -q` gave 3 failed, 349 passed,
  12 xfailed; the 3 failures are the environment-dependent hydra tests, and 349 is that
  environment's baseline (348) plus the new test.
- `uvx 'pre-commit<4.0.0' run --all-files`: all 13 hooks passed.
- Not tested: a real all-failed race on a cluster.

**Toolkit**
- Tool: `pytest` and `pre-commit` through `uv`/`uvx`, and the one-line repro.
- What it produced: fail-then-pass for the new test and `exit code: 0` then `1` for the repro.
- Difficulties: the hydra tests passed in one terminal and failed in another, so "the baseline" was
  two different numbers.
- What changed: recorded both results and compared each run to the baseline from the same
  environment.

---

## Implementation Notes

### 2026-11-13

Graded #257 with the scout (accept, straightforward), drafted the claim, and had repro-check grade
it; it caught a wrong sentence, which I removed. Auto mode blocked Claude Code from posting, so I
ran the voice guard by hand (pass) and posted the claim from Terminal. Synced my fork, branched from
`origin/master` @ 15f430d, reproduced `exit code: 0`, and pushed the failing test (59fccd9).
Plan-check rejected my first plan for stating an open question as settled; I added a risks section
and it accepted. Applied the fix in `main()` (8d20fcb) and the docs line (9c4ee63); repro now prints
`exit code: 1`. The ship-gate stopped because my local `master` was a week behind, so the diff
looked like 20 files; I fast-forwarded it with `git branch -f master origin/master`, and the gate
passed. Opened PR #289 through Claude Code.

**Toolkit**
- Tool: git, `gh`, and Claude Code in Terminal.
- What it produced: three small commits, one theme each, as AGENTS.md asks.
- Difficulties: `gh repo sync` and `git fetch` don't move a local `master` branch you aren't on.
- What changed: I now update the local base branch before running the ship-gate.

---

## Pull Request

**PR Link:** https://github.com/mila-iqia/cluv/pull/289

**PR Description:** Follows the repo template (Summary, Issues). Explains that `main()` ignored
`submit()`'s `None`, so the CLI exited 0; says the fix exits 1 only for `submit`, keeps `submit()`
unchanged for the Hydra launcher, and adds a docs line; shows the before/after repro; lists the
failing-first test commit, the suite result with the hydra tests explained, and pre-commit; says the
coverage is mocked only; asks whether the maintainer prefers `submit()` to raise; discloses Claude
Code use; ends with `Fixes #257`.

**Maintainer Feedback:**

- 2026-11-13: automated Copilot review recommended approval, no issues found. No maintainer feedback
  yet; CI is waiting for a maintainer to approve running workflows for a first-time contributor.

**Status:** Awaiting review

**Toolkit**
- Tool: the ship-gate (`check_gate.py --branch --base master --title ... --body-file pr_draft.md`),
  then `gh pr create` through Claude Code.
- What it produced: a first STOP (stale local master), then PASS on all 3 repo checks and all 5
  pr-precheck checks; PR #289.
- Difficulties: the first STOP looked like a broken branch until pr-precheck itself suggested a
  stale local master.
- What changed: fast-forwarded local `master` before re-running the gate.

---

## Learnings and Reflections

<!-- Rewrite in your own words. -->

### Technical Skills Gained

How a Python CLI turns a function's return value into a process exit code; testing a CLI entry point
by patching a coroutine with `mock.AsyncMock(spec=...)` and asserting on `SystemExit`; keeping a fork
and a local base branch in sync before comparing diffs.

### Challenges Overcome

A plan that read as settled while the maintainer hadn't answered; tests whose results changed with
the environment; and a ship-gate stop caused by a stale local branch rather than my code.

### What I'd Do Differently Next Time

Update the local base branch right after syncing the fork, and write the risks section into the
plan from the start instead of after plan-check asks for it.

---

## Resources Used

- cluv's AGENTS.md (docs and small-commit rules): https://github.com/mila-iqia/cluv/blob/master/AGENTS.md
- The issue: https://github.com/mila-iqia/cluv/issues/257
- Open PR #232, to check it doesn't overlap: https://github.com/mila-iqia/cluv/pull/232
- `unittest.mock.AsyncMock` docs: https://docs.python.org/3/library/unittest.mock.html#unittest.mock.AsyncMock
