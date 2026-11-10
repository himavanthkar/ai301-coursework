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

**Unit 5: [one line on the tool work]**

[2-4 sentences: what you built (the scout, your strategy), what broke
or fell short, what changed because of it.]

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

[1-2 paragraphs in your own words: how you searched, what your scan
surfaced, what your scout's verdict said, why this issue fits your
profile, and what you hope to learn. If your scout's verdict on it
was reject and you chose it anyway, say so here and say why; choosing
against your own tool silently is what loses points. Close with an
honest read of the tool itself: where the scout fell short on a real
wild search and what you changed or would change, or what convinced
you it held up. **Record the link to your claim comment in this section too.**
Not claimed yet? Write `claim pending` and today's date where the link goes,
and add the link the moment your claim lands.]

**Claim comment:** https://github.com/mila-iqia/cluv/issues/252#issuecomment-6750788609 (posted 2026-11-09)

**Toolkit**

- Tool: issue-scout (your scout: your search strategy, your rubric),
  and how you used it
- What it produced: [the scan and its verdict on this issue, described]
- Difficulties: [where it fell short, or "none met" if none did]
- What changed: [what you patched or would update, or "nothing"]

---

## Understanding the Issue

**Problem Description**

[In your own words, what is broken or missing?]

**Expected Behavior**

[What should happen?]

**Current Behavior**

[What actually happens?]

**Affected Components**

[Which parts of the codebase are involved?]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Reproduction Process

**Environment Setup**

[What a stranger would need to stand this up: your operating system,
the relevant versions (language, runtime, package manager, the project
itself), and how you set the project up. Then the challenges you faced
and how you solved them, or that none came up.]

**Steps to Reproduce**

1. [Step 1]
2. [Step 2]
3. [Observed result]

**Reproduction Evidence**

- **Commit showing reproduction:** https://github.com/himavanthkar/cluv/commit/0e7714dd262756fc1236e0168b27e8c33e78a20b (adds `test_bar_shows_used_and_total_size`, which fails on unmodified `master`)
- **Screenshots/logs:** before the fix, `_bar(40, 75)` / `_bar(148, 500)` printed
  `▰▰▰▰▰▱▱▱▱▱   53%` / `▰▰▱▱▱▱▱▱▱▱   30%`; the new test failed with
  `AssertionError: assert '148/500 GiB' in '▰▰▱▱▱▱▱▱▱▱   30%'` (1 failed, 1 passed).
- **My findings:** [what you discovered during reproduction; an
  honest, evidenced cannot-reproduce that led you to re-pick or ask
  the maintainer belongs here and costs you nothing]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Solution Approach

**Analysis**

[Your analysis of the root cause: what is causing the issue, traced
to the files and functions involved.]

**Proposed Solution**

[High-level description of your fix approach, and what you are
deliberately NOT changing.]

**Implementation Plan**

1. [Modify file X to do Y]
2. [Add or change Z]
3. [Update tests]

[Record deviations here as they happen: what changed mid-build and
why. A recorded deviation is honest work; silent drift is not.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Testing Strategy

[What observable checks prove the fix: your reproduction steps re-run
against the built change (before and after), and the repo's own test
suite or checks with the outcome shown. "Tests pass" with nothing
observable named is not a strategy.]

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

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

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Pull Request

**PR Link:** https://github.com/mila-iqia/cluv/pull/280

**PR Description:** [what the PR says; much of the content above
adapts into it]

**Maintainer Feedback:**

- 2026-11-09: automated Copilot review recommended approval, no issues found. No maintainer feedback yet.

**Status:** Awaiting review

**Toolkit**

- Tool:
- What it produced:
- Difficulties:
- What changed:

---

## Learnings and Reflections

**Technical Skills Gained**

[What you learned technically, named concretely.]

**Challenges Overcome**

[What was hard and how you solved it; point at the real events, the
Toolkit blocks above are full of them.]

**What I'd Do Differently Next Time**

[Reflection on your process.]

---

## Resources Used

[What actually helped, not a reading list. List things this story
already shows you using: the doc you followed to set the environment
up, the thread that explained the bug, the person who answered you.
One real entry beats ten plausible ones.]

- [Link to documentation that helped]
- [Discussion, issue, or post that helped]
