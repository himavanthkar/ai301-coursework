---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

Answer one question about one PR package: is this pull request ready to submit? A PR package is a
candidate pull request — its title, its description, its commits, its diff, and its test evidence —
read against the plan it claims to implement (including that plan's deviation notes) and the issue
the plan belongs to. Do not grade the code's general quality, the plan's merits, or anything outside
this one package, and do not grade more than one package per run.

## Inputs and modes

Run in exactly one of two modes.

**Live mode** (the student checking their own PR before opening it). Read these inputs, and only
these:

1. The plan: the `plan.md` file the student names, including everything under its `## Deviations`
   heading. A house-chain student reads the house plan and house repro pack instead.
2. The diff: every committed change the branch makes relative to the default branch, produced by
   running `git diff main...HEAD` (three dots) from the working copy. Also run
   `git diff main...HEAD --stat` and `git log main..HEAD --oneline` for the file list and commits.
   Uncommitted changes are not part of the PR; do not grade them.
3. The draft PR title and description: the file the student names (normally `pr_draft.md`; its
   first line is the title, the rest is the description).
4. The test evidence: the file the student names (normally `test_evidence.md`).
5. The issue: the issue URL the student gives, read through `gh`, the GitHub API, or the web: the
   issue body and the full comment thread, including the student's own posted repro and plan
   comments.
6. The repo's stated asks: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md` in the
   working copy (and any AI-use policy they link to).

**Eval mode** (grading a package bundle). The bundle is the whole world. Every fact comes from the
bundle text: its repo-facts block, issue, thread highlights, plan context, and candidate PR (title,
description, commits, diff, test evidence). Fetch nothing, run nothing, read nothing else. Eval mode
always grades a complete package: every check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` in this skill directory before anything else. Confirm the issue URL
and the PR's target repo match the repo on its `Repo:` line; if they do not, refuse to grade and say
which repo the scope allows. If the `Repo:` line still shows a bracketed placeholder, stop without
grading and tell the student to fill the `Repo:` line in `scope.md` with their section's Path Review
repo; never guess a scope. Apply the house rules scope.md states wherever they change how evidence
is read (for example, a classmate's PR on the same issue never counts against this one). In eval
mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md` in this skill directory and hold the outgoing text — the
draft PR title and description — against each of its rules. In the readable summary, list every rule
the draft breaks, quoting the rule and the offending line. The voice guide never changes the verdict
on its own, because it holds the student's personal rules, not the rubric's checks. In eval mode,
ignore `voice-guide.md` entirely.

## Component reads

Read `rubric.md`: its checks table defines what gets graded, and its verdict rule defines how grades
become the verdict. Read `references/evidence-guide.md`: it maps where each evidence family lives in
an eval bundle and in a live PR, and what good looks like there. Then execute `procedure.md` as
written: it fixes the read order, how evidence is gathered, how each check runs, and how the verdict
is assembled.

Where `procedure.md` is silent on a step you need, do not improvise: note the gap in the summary,
then grade from the rubric's stated pass condition alone. If `rubric.md` has no checks or no verdict
rule, or `procedure.md` has no steps (only template comments), refuse to grade and say which file is
empty; a tool that invents checks at runtime produces noise that looks like judgment.

## Verdict and output

The verdict is binary: `accept` means the PR is ready to submit; `reject` means hold it. There is no
third verdict and no score; put reservations in check evidence lines, never in the verdict. Before
the JSON you may print a short readable summary (one line per check, plus voice-guide notes in live
mode). End the reply with the fenced JSON block below, valid, last, and with nothing after it:

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or quote that decided it. "Looks
  fine" is not evidence.
- Grade the thing, not the polish: read the diff against the plan, the evidence against the test
  plan, the description against the diff, and the PR against the repo's stated asks. A terse PR can
  be ready; a long confident one can be hiding drift.
- An honestly disclosed shortfall is not a failure: if the plan's deviation notes or the description
  say plainly what was left out or changed and why, grade the PR on what it says it does.
- The rubric decides, not the run: if a check passes by its stated condition but feels wrong, it
  passes; note the tension in the summary.
- The procedure decides how: follow `procedure.md` as written and report its gaps.
- Unclear defaults to fail: where the rubric's verdict rule does not say otherwise, a check graded
  `unclear` counts as failed, because a PR you cannot verify from the package is not ready to submit.
