# Contribution: Every published rate is a point estimate with no confidence interval

**Contribution Number:** 4
**Student:** Himavanth Karpurapu
**GitHub Username:** himavanthkar
**Issue:** https://github.com/wehnsdaefflae/llmsectest/issues/36
**Pull Request:** not opened yet (waiting on the owner's answer to my question)

<!--
Extra contribution, not graded. Sections are filled only as far as the work has gone.
Sections marked REWRITE are drafts for me to put in my own words.
-->

---

## Toolkit

### 2026-11-14 to 2026-11-15: shortlist, claim, setup

I ran my `oss-contribute` skill, Phase 1 to Phase 3. The first scout grade (2026-11-14) and the
first repro-check on the claim draft were in-session judgments: the working Claude session graded
against the rubric itself. I had both re-run as separate `claude -p` runs on 2026-11-15, and
those are the results recorded below. The voice guard was a real run of `check_voice.py` both
times. Because of this I added ground rule 8 to `oss-contribute`: tool checks must be real
separate runs, never an in-session judgment.

---

## Why I Chose This Issue

> REWRITE: this section is a draft for me to put in my own words.

This came from the same shortlist as cq#393 (`ai_ml_issues.csv`, 780 rows, 24 issues given the
full Phase 1 checks, 9 survivors). It ranked first for fit: Python, a security test tool for LLM
applications, one function, a stated definition of done, no hardware. While cq#393 waits on a
design reply, this is the small one I can move in parallel. The repo is small and has a single
maintainer, so it is less well known than the others on the list.

Health checks on 2026-11-14: CONTRIBUTING.md and the PR template state no AI policy; the owner
merges outside PRs (PR #34 from an outside contributor is the model change the issue points to).

**Toolkit**
- Tool: issue-scout, direct mode. Separate `claude -p` run on 2026-11-15 (after my claim; the
  rubric ignores my own claim).
- What it produced: verdict **accept**, difficulty "straightforward". Recent commits: pass
  (latest 2026-11-16 UTC, PR #43). Maintainer responsive: pass (owner commented on and merged
  PR #43 the day it was opened). scope-feasible: pass. Not claimed: pass (no assignees, no linked
  PRs, only my own comment). AI-friendly policy: pass (nothing stated). Good first issue label:
  pass.
- Difficulties: the first grade on 2026-11-14 was an in-session judgment, not a tool run. In the
  separate run, the label row names `good-first-issue` with hyphens while the repo spells it with
  spaces.
- What changed: a dated line in the scout's `scan-notes.md` about the label spelling; ground
  rule 8 in `oss-contribute`.

---

## Understanding the Issue

> REWRITE: this section is a draft for me to put in my own words.

### Problem Description

Reports publish a rate per OWASP category (the issue's example is "LLM02 ... 12/43") as a bare
point estimate, with nothing saying how uncertain it is.

### Expected Behavior

A rate computed over at least 30 samples carries a lower and an upper bootstrap bound. One
computed over fewer says so and carries no bound. A test pins the interval for a fixed input with
a fixed seed. The docstring states the method, the number of resamples and the confidence level.
Existing fields keep working.

### Current Behavior

Each `by_category` row from `attack_tally` holds counts only (`attempted`, `withstood`,
`findings`, `inconclusive`, `undelivered`, `boundary_refused`, `voided`, `delivered`,
`corpus_cases`). No rate and no bound is stored.

### Affected Components

- `src/llmsectest/reporting/statistics.py` (`attack_tally`)
- `src/llmsectest/reporting/sarif_generator.py` (embeds the tally verbatim)
- `tests/test_attack_tally.py`
- `CHANGELOG.md` (the PR template asks for a bullet under `[Unreleased]`)

**Toolkit**
- Tool: `gh issue view`, then reading and `grep` on a clone of `main` at c0fad33.
- What it produced: the open question in my claim. The issue's "12/43" reads as a count across
  applications, but `attack_tally` sees one run, and the test docstring puts the shipped corpus
  at 4 cases for LLM02 and 13 for LLM01. So a single run's row will often be under 30 samples.
  I could not find where the cross-application rate is assembled in `src/`.
- Difficulties: the issue's paths start with `code/`; in the repo they are `src/` and `tests/`.
- What changed: nothing in the tools; I noted the path difference in the claim.

---

## Reproduction Process

### Environment Setup

- Fork: https://github.com/himavanthkar/llmsectest (`origin`); wehnsdaefflae/llmsectest is `upstream`.
- Branch: `feat/36-bootstrap-ci` from `upstream/main` at f6b118e.
- Install: `uv venv .venv`, then `uv pip install -e ".[dev,cvss]"` (CONTRIBUTING's command, run
  through uv). macOS, Python 3.14.3.
- Baseline, 2026-11-15, at c0fad33: `ruff check src tests examples` printed "All checks
  passed!"; `coverage run -m pytest -q` gave 1070 passed; `coverage report` total 88%.
- Baseline, 2026-11-15, at f6b118e (upstream moved by one commit, PR #43, which does not touch
  `statistics.py`): ruff "All checks passed!"; 1073 passed; coverage total 88%.

### Steps to Reproduce

This is a feature request, so there is no failing behavior. What I confirmed: `grep` for
`bootstrap` and `confidence_lower` in `src/` finds nothing in the reporting tally, and the row
dictionary in `attack_tally` has no rate or bound field.

### Reproduction Evidence

- **Commit showing reproduction:** none; no code is written yet.
- **My findings:** see Understanding the Issue.

**Toolkit**
- Tool: repro-check, claim-only mode, separate `claude -p` run on 2026-11-15 before posting;
  then the voice guard.
- What it produced: repro-check **accept**. AI disclosure compliance: pass. Claim comment
  respects conventions: pass. The four repro checks: unclear, "not yet applicable: claim-only
  draft". It re-checked the claim's facts against the code with line numbers. Voice guard:
  `voice-guard: pass. Your rules hold; you can post it.`
- Difficulties: my first repro-check result was an in-session judgment; I had it re-run as a
  real tool run before posting. Claude Code's permission classifier blocked the `gh issue
  comment` command, so I ran it myself in a terminal.
- What changed: ground rule 8 in `oss-contribute`.

**Claim:** https://github.com/wehnsdaefflae/llmsectest/issues/36#issuecomment-6909753393
(posted 2026-11-16T07:02:04Z; the thread had no comments and no assignee before it).

---

## Solution Approach

Not started. I am holding fix code until the owner answers which rate should carry the bounds.
My stated default: findings over scored probes (`attempted` minus `inconclusive`) per row, with a
percentile-bootstrap helper using the standard library's `random.Random(seed)`, because the
package's only runtime dependency is `pytest`.

---

## Testing Strategy

Not started.

---

## Implementation Notes

### 2026-11-15

Claim drafted, checked and posted. Fork, branch and baseline done. No fix code written.

---

## Pull Request

**PR Link:** none yet

**Maintainer Feedback:**

- 2026-11-15: none yet.

**Status:** Claimed; waiting on the owner's answer.

---

## Learnings and Reflections

> REWRITE: for me to write in my own words once there is something to reflect on.

---

## Resources Used

- Issue: https://github.com/wehnsdaefflae/llmsectest/issues/36
- llmsectest CONTRIBUTING.md and PR template
- PR #34, the model change named in the issue: https://github.com/wehnsdaefflae/llmsectest/pull/34
