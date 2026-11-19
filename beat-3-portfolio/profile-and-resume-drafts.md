<!-- DRAFTS prepared with Claude Code from my own record (PR #289, contribution-story-2.md).
     REWRITE in my own words before using. Nothing here is posted anywhere yet. -->

# Drafts from the unit 9 session notes

## 1. First comment under the LinkedIn post (tags CodePath so the alumni team can reshare)

Thank you @CodePath. I did this work in AI301, the AI Open Source Capstone.

(Type the @ and pick CodePath from the list so it becomes a real tag.)

## 2. LinkedIn profile

Headline shape from the session: what I am + what I contribute to + what I want next.

[What I am] | Open source contributor in Python (mila-iqia/cluv) | Seeking [role and start date]

Featured pins, in this order: the LinkedIn post, the technical write-up, the pull request
(https://github.com/mila-iqia/cluv/pull/289).

Projects section: one entry for the contribution and one for the toolkit, with the same words
as the resume entries below.

## 3. GitHub profile README (top of himavanthkar/himavanthkar)

I write Python and TypeScript and contribute to open source AI and ML tooling.

- Merged: [mila-iqia/cluv#289](https://github.com/mila-iqia/cluv/pull/289), `cluv submit` now exits 1 when every job submission fails
- Write-up: [link once the write-up is published]
- In review: [mila-iqia/cluv#280](https://github.com/mila-iqia/cluv/pull/280), `cluv status` shows used/total storage size next to the percentage

## 4. Resume entries (each bullet starts with what I did and ends with a result or number)

**Open source contributor, mila-iqia/cluv** (Python CLI for running jobs on compute clusters) | github.com/mila-iqia/cluv/pull/289

- Fixed `cluv submit` reporting success when every job submission failed, so scripts and CI that call it now get exit code 1 instead of 0
- Reproduced the bug with a failing test before changing any code, then shipped the fix in 3 files (+21/-2)
- Merged on 2026-11-16 after 2 maintainer approvals with 0 changes requested

**Contribution toolkit for Claude Code** (4 skills, 2 hooks, 1 PR monitor) | [repo link if public]

- Wrote rubrics that grade each step of a contribution (issue choice, reproduction, plan, pull request) before anything is posted
- Built a ship-gate hook that runs the checks before a pull request can be opened, which stopped a PR whose diff showed 20 files of other people's work
- Caught 3 of my own mistakes on cluv#289 before a maintainer saw them
