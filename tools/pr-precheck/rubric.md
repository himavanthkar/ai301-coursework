# Rubric: is this pull request ready to submit?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diff matches plan | Every changed file and hunk in the diff, read against the plan's stated scope, files, and approach plus its deviation notes; and every change the plan promises, read against the diff | Every hunk implements something the plan names or a recorded deviation explains, and everything the plan promises is in the diff unless the plan's deviation notes or the PR description say plainly that it was left out and why. Fails on an unplanned feature, option, rewrite, rename pass, or behavior change with no deviation note, and on a planned change that is silently missing | required |
| Description matches diff | The PR title and description's claims about what the change does, read against the diff | Every claim the description makes about what was changed is visible in the diff, and the description does not claim the plan was followed in full when the diff does less or more. Fails when the description says something was done (a docs update, a test, a second fix) that the diff does not contain, or omits a substantive change the diff does make | required |
| Decisive test evidence | The test evidence (and any results quoted in the description), read against the plan's test plan and the issue's trigger condition | The evidence shows the issue's own trigger (the repro or the exact failing case the plan targets) re-run, with an observable before and after (concrete output, exit code, or a named failing-then-passing test), on the path the fix changes, plus the result of at least one named test or check command. Fails on "works now", "tested locally", "tests pass" with nothing named, evidence that only runs a control or the unchanged path, or no before/after at all. A check reported honestly as failing or unavailable, with the reason, still counts as evidence | required |
| Clean diff | The diff's added and removed lines | The diff contains only the change and its tests. Fails on debug output (prints, logs, eprintln added to investigate), commented-out code, formatting or whitespace churn in lines the change does not otherwise touch, or hunks unrelated to the fix (import restructuring, drive-by cleanups). Explanatory comments that describe the fix are not debris | required |
| Repo standards met | The repo-facts block's (live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`) stated PR template, checklist, and AI-use policy, read against the PR title and description | Every section or checklist item the repo's template requires is present with real content (not left blank or as template placeholder text), and, when the repo's stated policy requires disclosing AI assistance, the description discloses it. When the repo states no template or AI policy, this check passes on those points | required |

## Verdict rule

Accept only if every required check passes. Reject if any required check fails or is graded
`unclear`: an unclear grade counts as a fail, because a PR that cannot be verified from the package
is not ready to submit. There are no preferred checks; each check covers one failure family that is
enough on its own to hold a PR. A shortfall the plan's deviation notes or the description disclose
honestly does not fail "Diff matches plan" or "Description matches diff".
