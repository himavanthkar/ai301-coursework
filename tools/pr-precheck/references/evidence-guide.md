# Evidence guide: where evidence lives in a PR package

## Plan fidelity (harness category: silent-drift)

Where it lives: in an eval bundle, the "Plan context" block (the plan's scope, files, out-of-scope
list, deviation or deferral notes) read against the candidate PR's "Diff" and "Commits" sections and
the "Description". In live mode, `plan.md` (its Scope, Files, Approach, and `## Deviations` sections)
read against `git diff main...HEAD`, `git diff main...HEAD --stat`, `git log main..HEAD --oneline`,
and the description in `pr_draft.md`.

What good looks like: every hunk in the diff traces to a change the plan names or to a deviation note,
and every change the plan promises appears in the diff or is named as left out, with a reason, in the
deviation notes or the description. The description's account of the change ("adds X, updates Y, per
the plan") matches the diff line for line. Drift looks like: a new config option, flag, feature, or
rename pass the plan never mentioned; a rewrite of a neighboring function; or a description saying
"implements the plan" when a promised part (a docs update, a second fix) is not in the diff. An
honestly disclosed shortfall is not drift.

## Test evidence (harness category: not-tested)

Where it lives: in an eval bundle, the candidate PR's "Test evidence" section and any Testing section
of the "Description", read against the plan's test plan in "Plan context" and the trigger in the
"Issue". In live mode, `test_evidence.md` and the Testing section of `pr_draft.md`, read against the
Test plan in `plan.md` and the repro in the student's posted repro comment.

What good looks like: the issue's own trigger, or the exact failing case the plan names, re-run with
observable before and after output (a printed value, an exit code, a failing-then-passing named test),
on the code path the fix changes; plus at least one named test or check command with its result (for
Path Review: `make test-unit`, `make test-integration`, `make lint`, `make typecheck`). A check that
honestly failed or could not run, reported with its reason, counts. Not-tested looks like: "tested
locally", "works on my machine", "all tests pass" with no command or output; evidence that only runs
the control case or an input that never triggered the bug; or an after with no before.

## Diff quality (harness category: unreviewable)

Where it lives: in an eval bundle, the added (`+`) and removed (`-`) lines of the candidate PR's
"Diff". In live mode, the output of `git diff main...HEAD`.

What good looks like: every added or removed line is part of the fix or its test. Explanatory
comments that say why the fix works are fine. Unreviewable looks like: debug prints or logs
(`print`, `console.log`, `eprintln!("DEBUG ...")`); commented-out code (old lines left behind as
comments, or new code disabled with `//` or `#`); formatting or whitespace churn on lines the fix
does not otherwise touch; and hunks unrelated to the fix, such as import reordering or drive-by
cleanups in other functions.

## Standards and comms (harness category: standards-wall)

Where it lives: in an eval bundle, the repo-facts block's "pull requests" line (the template's
required sections and checklist) and "contribution policy" line (including any AI-use policy), read
against the candidate PR's "Title" and "Description". In live mode, the repo's
`.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`, read against `pr_draft.md`; Path
Review's house rules in `scope.md` also require every template section filled and the AI-use
disclosure in Notes for Reviewers.

What good looks like: every section or checklist item the template requires is present with real
content (a "Closes #N" line where asked, ticked boxes only where true), and when the stated policy
requires disclosing AI assistance, the description says which tool was used and for what. A repo
with no template or AI policy imposes nothing here. Standards-wall looks like: a required checklist
missing or left as placeholder text, or no AI disclosure in a repo whose policy demands one, even when
the fix itself is excellent.
