<!-- DRAFT prepared with Claude Code from my own record (contribution-story-2.md, PR #289).
     REWRITE: I need to put every section in my own words. -->

# Making `cluv submit` report failure: a three-line fix, and the checks that caught my mistakes on the way

## Overview

[cluv](https://github.com/mila-iqia/cluv) is a command-line tool from Mila for running jobs on
compute clusters. Issue [#257](https://github.com/mila-iqia/cluv/issues/257) reported that
`cluv submit` exited with code 0 even when every job submission failed, so a script or CI job
calling it saw success. I reproduced the bug, wrote a test that failed because of it, and fixed it
in `main()` so the command exits 1. My pull request,
[mila-iqia/cluv#289](https://github.com/mila-iqia/cluv/pull/289), was approved by two maintainers
and merged on 2026-11-16. I also wrote the tools that checked each step: a contribution process
(`oss-contribute`) with my own rubrics for the claim, the plan and the PR, and a ship-gate that
runs before a PR is opened. This write-up covers both.

## What happened

### 1. Claiming the issue (and a wrong sentence)

I read the thread and the code, then drafted a claim comment. Before posting, I ran my repro-check
tool on the draft. It accepted the claim but flagged one sentence I had written: "other failed
`cluv submit` runs would exit 1". That was wrong. `submit()` has only one `return None`, the case
where every submission failed, so my fix could not affect other failures. I checked the code
myself, removed the sentence, and posted the
[claim](https://github.com/mila-iqia/cluv/issues/257#issuecomment-6848675014).

### 2. Reproducing the bug

I have no cluster access, so I reproduced at the function level by faking `submit()` returning
`None`:

```
uv run python -c "from unittest import mock; import cluv.__main__ as m; m.submit = mock.AsyncMock(spec=m.submit, return_value=None); m.main(['submit', 'first', '--', 'python', 'main.py'])"; echo "exit code: $?"
exit code: 0
```

Then I wrote a test and committed it alone
([59fccd9](https://github.com/mila-iqia/cluv/pull/289/commits/59fccd9)). On the unfixed code it
failed with `Failed: DID NOT RAISE SystemExit` (`1 failed in 1.25s`):

```python
def test_submit_exits_nonzero_when_every_submission_fails(
    monkeypatch: pytest.MonkeyPatch,
) -> None:
    """`cluv submit first` exits 1, not 0, when every racing job failed (#257)."""
    monkeypatch.setattr(
        cluv_main, "submit", mock.AsyncMock(spec=cluv_main.submit, return_value=None)
    )

    with pytest.raises(SystemExit) as exc_info:
        cluv_main.main(["submit", "first", "--", "python", "main.py"])

    assert exc_info.value.code == 1
```

### 3. The plan my own tool rejected

My first plan said the fix belonged at the CLI level as if that was decided. But my claim had
asked the maintainer that exact question and nobody had answered. My plan-check tool rejected the
plan on its "Honest unknowns" check, and also because the test baseline I wrote did not match the
tool's own test run. Three hydra tests passed in one terminal and failed in another.

What I changed: I added a "Risks and unknowns" section to the plan (the open question, my choice
of exit code 1, the mocked-only coverage, the environment-dependent tests) and recorded both
baselines instead of one. The second run of plan-check accepted it.

### 4. The fix

`submit()` already documents "Returns None if the submission failed." The bug was that `main()`
threw the result away. The fix keeps the result and exits 1 only for `submit`:

```diff
-            asyncio.run(function(**args_dict))
+            result = asyncio.run(function(**args_dict))
...
+    if subcommand == "submit" and result is None:
+        # `submit` returns None when every submission failed, e.g. in a `first` race (#257).
+        sys.exit(1)
```

After the fix the same repro printed `exit code: 1` and the new test passed. The whole PR is
3 files, +21/-2: the test, the fix, and one docs line.

### 5. The ship-gate stopped a PR that looked like 20 files

My ship-gate runs my pr-precheck rubric before `gh pr create`. The first run stopped: it failed
"Diff matches plan", "Description matches diff" and "Clean diff", because the diff showed 20
files of other people's work. My branch was fine. My local `master` was a week behind, so the
gate compared against the wrong base. After `git branch -f master origin/master` the diff was my
3 files and the gate passed. I added that sync step to my process so it runs before every new
branch.

### 6. What landed

Two maintainers, notoraptor and hvdbm, approved the PR with no change requests. hvdbm merged it
on 2026-11-16 (merge commit 141f113) and issue #257 closed as completed.

## Limitations and what's next

- **The reproduction is faked.** I never ran a real race where every job fails on a cluster. The
  test replaces `submit()` with a mock that returns `None`, and I said so in the PR.
- **One CI job was red.** `integration_tests` showed a failure on the PR at merge time. The
  maintainers merged anyway and later runs on `master` passed, but I have not investigated it.
- **My tools were not always run as tools.** On a later contribution I found that some checks had
  been judged inside the working Claude session against the rubric, not run separately. I re-ran
  them and added a rule: tool checks must be real separate runs, and I report their actual output.
- **A gap in my issue scout.** It has no check for "the design must be agreed first", so it
  accepted an issue that is blocked on an RFC.

Next: my second cluv PR, [#280](https://github.com/mila-iqia/cluv/pull/280), is waiting for
review. I have claimed two more issues (bootstrap confidence intervals in llmsectest, OIDC token
verification in mozilla-ai/cq) and I am waiting for the maintainers' answers before writing code.
I will also add the missing "design first" check to the scout's rubric.
