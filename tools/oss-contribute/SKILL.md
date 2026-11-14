---
name: oss-contribute
description: Run Himavanth's end-to-end open-source contribution process (find, claim, reproduce, plan, fix, PR, follow up, record) using the AI301 toolkit skills and hooks. Use when starting a new contribution, continuing one in progress, or checking on open PRs.
---

# oss-contribute: my contribution process, start to finish

This is the process I built in CodePath AI301 (units 1-8) and used for mila-iqia/cluv#252 (PR #280)
and #257 (PR #289). Follow it in order. Each phase names the tool that checks it; a phase is done
only when its tool passes.

## Ground rules (never break these)

1. **Never invent output.** Every number, test result, or quote in a file or post must come from a
   command you actually ran in this session. If something can't be run, say so in writing.
2. **Stop and ask me before anything public**: posting a comment, opening or editing a PR, closing
   anything. Show me the exact text and the exact command first. Pushing to MY fork is fine.
3. **One open claim per repo at a time.** Never claim an issue we aren't starting this week.
4. **Quality over count.** Every PR is public under my name and gets read by maintainers and by
   people reviewing my applications. Skip issues that are "easy points" (translations, add-your-name,
   copy-paste test issues, typo-only fixes in hobby repos).
5. **Read every comment on the issue before claiming.** If anyone asked to work on it, it's taken,
   even with no assignee.
6. **Disclose AI use** in every PR description, in my own words, even if the repo doesn't require it.
7. **Respect the repo's rules over mine**: CONTRIBUTING.md, AGENTS.md, CLAUDE.md, the PR template,
   and its AI policy. If the repo doesn't accept AI-assisted contributions, don't contribute there.

## My setup (where things live)

- Work folder: `~/Desktop/open-source-contributions/` (one clone per repo, cloned from MY fork).
- Course repo: `~/Desktop/"all codepath ai capstone projects"/ai301-coursework` (stories go in
  `beat-2-contribution/`).
- Skills in `~/.claude/skills/`: `issue-scout`, `repro-check`, `plan-check`, `pr-precheck`.
- Hooks: voice guard `~/.claude/voice-guard/` (checks every outgoing comment/PR body), ship-gate
  `~/.claude/ship-gate/` (runs checks + pr-precheck before `gh pr create`).
- PR monitor: `~/.claude/pr-monitor/monitor.py` with my playbook `playbook.md`.
- Voice guide: `~/.claude/voice-guard/voice-guide.md` (my rules for claims, questions, pings).

Phase 0, check the toolkit is installed: `ls ~/.claude/skills ~/.claude/voice-guard ~/.claude/ship-gate ~/.claude/pr-monitor`
and `gh auth status`. If something is missing, tell me before going further.

## Phase 1: Find an issue

1. **Same project first.** Look for a next issue in a repo where I already have a PR, so maintainers
   see my name again. Move on if that repo went badly (no response to reviews, wrong fit).
2. **Other sources**: issues I paste from the AI301 Issue Selection site (it needs my login; I'll
   paste the list), and `gh search issues` with `--label "good first issue" --state open --no-assignee
   --limit 100` plus `--language python` or `--repo <org/repo>`. Never put several keywords in one
   quoted string: `gh` treats it as an exact phrase and returns nothing.
3. **My fit**: Python; AI/ML infrastructure, evals, ML tooling, CLIs; small, clearly described issues
   with a stated expected result that I can test without special hardware or cluster access. Avoid
   big refactors, issues waiting on design decisions, C++/Java/C#, and issues that need hardware.
4. **Health checks, before grading** (look at the real pages, quote what you find):
   - Recent commits on the default branch.
   - Five recent MERGED PRs by outside contributors (skip maintainers and bots): days from opened to
     first human review, and what reviewers asked for. Outside PRs left with no review is a warning.
   - CONTRIBUTING.md, AGENTS.md, CLAUDE.md, PR template, AI policy, test and lint commands.
   - The issue thread: claims, linked PRs, maintainer comments.
5. **Grade with the scout**: `claude "issue-scout: grade <issue URL>"`. Record the verdict and the
   difficulty note. Choosing against a reject is allowed only with a written reason.
6. Append a dated line to `~/.claude/skills/issue-scout/scan-notes.md` for anything the scout got
   wrong (example: it once failed "Not claimed" on my own claim, so that row now ignores my account).

## Phase 2: Claim

1. Write `claim-draft.md` (in `~/Desktop/open-source-contributions/ai301-unit6-starter/` or the clone):
   - Say I'd like to work on it, what I found from reading the code, and my first concrete step.
   - A loose window ("I'll aim to open a PR within a few days"), never a deadline.
   - At most one question, with my default stated ("Unless you'd prefer X, I'll go with Y").
   - Every technical sentence verified against the code. (repro-check once caught a wrong claim.)
2. `claude "repro-check: grade my draft claim comment in claim-draft.md for issue <URL>"` until accept.
3. Re-check the thread is still unclaimed.
4. Voice guard: `python3 ~/.claude/voice-guard/check_voice.py --draft claim-draft.md` must say pass.
5. **Ask me**, then post: `gh issue comment <URL> --body-file claim-draft.md`. Save the comment link.
   (Claude Code's auto mode may block external writes; then I run it.)
6. If I clicked Claim on the Issue Selection site for other picks, remind me to Unclaim them.

## Phase 3: Set up the repo

1. Fork and clone into the work folder: `gh repo fork <owner>/<repo> --clone`.
2. `gh repo set-default <owner>/<repo>` (the ORIGINAL repo, so PRs don't open on my fork).
3. Sync before every new branch:
   `gh repo sync <me>/<repo>`, `git fetch origin`, then
   `git branch -f <base> origin/<base>` (LESSON: a stale local base made the ship-gate see 20 files of
   other people's work), then `git switch -c <fix|feat>/<issue#>-<short-name> origin/<base>`.
4. Install deps exactly as CONTRIBUTING says (e.g. `uv sync`), then run the repo's own test command
   and record the BASELINE with the date and commit. Note any test that fails before my change, and
   check whether it's environment-dependent (cluv's hydra tests passed in one terminal and failed in
   another).
5. Write `CLAUDE.local.md` from the Unit 6 template (under ~60 lines: conventions, the subsystem my
   issue touches, build/test commands, house style, AI policy). Add `@AGENTS.md` as the first line if
   the repo has one. `echo CLAUDE.local.md >> .git/info/exclude`.
6. Keep working files out of commits: `plan.md`, `plan-comment.md`, `test_evidence.md`,
   `pr_draft.md`, `ship-manifest.md`, `claim-draft.md` go in `.git/info/exclude`. Check with
   `git status --short`.

## Phase 4: Reproduce

1. Find the smallest command that shows the bug on the unmodified base, and save its real output.
   If it needs hardware or access I don't have, reproduce at the function level and say so.
2. Write a test that fails because of the bug (follow how the repo's existing tests do it), run it,
   and confirm it fails for the right reason.
3. Commit the failing test ALONE ("Add failing test: ... (#N)") and push to my fork. That commit is
   the reproduction evidence.

## Phase 5: Plan

1. Write `plan.md` with these sections: Analysis (cause traced to files/functions/lines), Proposed
   Solution (and what I'm NOT changing), Implementation Plan (numbered steps), Test plan (exact
   commands and expected results), **Risks and unknowns** (open questions to the maintainer, my own
   choices, what I can't test, flaky/env-dependent tests), Deviations.
2. Write a short `plan-comment.md` (cause + fix + the open question). Do not post it.
3. `claude "plan-check: grade my plan in plan.md and draft comment in plan-comment.md for issue <URL>"`
   until accept. (It rejected a plan that stated an unanswered question as settled.)

## Phase 6: Build and test

1. Smallest change that fixes the cause. Follow the repo's style and AGENTS.md rules (e.g. update
   docs when behavior changes). One theme per commit, clear messages ending with `(#N)`.
2. Re-run: the repro (before vs after), the new test (fail then pass), the full test command
   (compare to the baseline from the SAME environment), lint/format (e.g. `uvx 'pre-commit<4.0.0' run --all-files`).
3. Write `test_evidence.md` with the real before/after outputs and check results.
4. Update `plan.md` Deviations if anything changed.

## Phase 7: Open the PR

1. `ship-manifest.md` (Unit 7 template): title is the ORIGINAL repo; 2-4 checks from CONTRIBUTING/CI
   with sources; checks that already fail on base go in the excluded table with date and reason.
2. `pr_draft.md`: copy the repo's PR template and answer every item. Must include: what changed and
   why, `Fixes #N`, before/after output, checks run with results, honest limits, one open question if
   any, and the AI-use sentence in my own words.
3. Make sure the local base is current (`git branch -f <base> origin/<base>`), then
   `python3 ~/.claude/ship-gate/check_gate.py --branch --base <base> --title "<title>" --body-file pr_draft.md`
   until `ship-gate: PASS`. No quotes, apostrophes, or backticks in the title.
4. Voice guard on `pr_draft.md` if posting outside Claude Code.
5. **Ask me**, then: `gh pr create --repo <owner>/<repo> --base <base> --head <me>:<branch> --title "<title>" --body-file pr_draft.md`.
   Confirm the printed link is on the ORIGINAL repo. Never open a second PR for the same issue.

## Phase 8: Follow up

1. `python3 ~/.claude/pr-monitor/monitor.py --state` on the PR's branch (free). Full run only when
   something new arrived.
2. Bot reviews (Copilot, codecov) need no reply and don't reset the clock; check WHO reviewed before
   trusting a FEEDBACK-IN state.
3. Nudge once, only after my playbook threshold (12 quiet days for cluv), tagging only the reviewing
   maintainer, no pressure.
4. Reviewer asks for changes: make them all, push once, then reply in my own words pointing at the
   commits, through the voice guard, and re-request review.
5. Don't click "Update branch" unless a maintainer asks or there's a conflict.

## Phase 9: Record it (as you go, not at the end)

1. Copy `beat-2-contribution/extra-round-template.md` to `contribution-story-N.md` in the course repo
   and fill each section the day it happens: claim link, repro commit, plan, tool results (passes AND
   rejects), commits, PR link, dated maintainer feedback, status.
2. Mark "Why I Chose", "Understanding the Issue", and "Learnings" for me to rewrite in my own words.
3. Commit and push the course repo (my git identity, no Claude co-author line).

## Lessons from my first two contributions

- Claims get taken within hours in popular AI repos (inspect_ai); smaller active repos like cluv are
  a better first stop. Watch good repos with GitHub "Watch -> Custom -> Issues".
- Tools caught real mistakes every time: scout (own-claim bug in my rubric), repro-check (a wrong
  sentence in my claim), plan-check (open question presented as settled; wrong baseline), ship-gate
  (stale local master). Treat a reject as information, fix the cause, re-run.
- Function-level reproduction with a faked dependency is fine when the real trigger needs access I
  don't have, as long as the PR says so plainly.
- Fix at the right layer: in cluv#257 the bug was `main()` ignoring `submit()`'s documented `None`,
  not `submit()` itself.
