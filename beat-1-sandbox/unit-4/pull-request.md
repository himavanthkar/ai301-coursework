# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s1/pull/212

**Branch**

fix/56-structural-chunker-no-headings

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run of these same skill files in a Claude Code cloud session, before I installed them on my
   Mac: 20/20 (bar PASS), categories clear-accept 7/7, not-tested 4/4, silent-drift 4/4,
   standards-wall 2/2, unreviewable 3/3. That session's eval-run.txt is not the one I committed.
2. Full run on my Mac with `--save-run eval-run.txt`, no file changes in between: 19/20 (bar PASS),
   categories clear-accept 6/7, not-tested 4/4, silent-drift 4/4, standards-wall 2/2,
   unreviewable 3/3. This is the committed file: "agreement: 19/20 scored items  (bar: 18/20: PASS)".

**Package analysis**

pkg-11 (ripgrep, keep a backslash-escaped trailing space when trimming gitignore lines). Gold label:
accept. My rubric decided reject in the committed run, with the table note "failed: Description
matches diff"; in run 1 the same files accepted it.

The description says: "The trim now stops at a space preceded by an odd number of backslashes,
matching git. One function touched, tests added for the escaped, unescaped, and double-backslash
cases." The diff changes `parse_gitignore_line` to call a new helper and adds that helper,
`fn trim_unescaped_trailing_spaces(line: &str) -> &str`, so read literally the diff touches two
functions, not one. My "Description matches diff" check fails a PR whose description claims
something the diff does not contain, and the most likely reading is that the grader took "One
function touched" as such a claim. The eval-run file records only the failed check's name, not the
grader's evidence line, so this is my reading of the package rather than a quoted reason. The gold
label treats the helper as part of the one change (it exists only to implement the fix the plan
names), and the rest of the package is strong: decisive before/after repro, `cargo test -p ignore`
named with a result, and the AI disclosure the repo's policy asks for. The two runs disagreeing on
identical files shows this package sits on the boundary of how literally my check reads a
description's wording.

**Check rationale**

| Description matches diff | The PR title and description's claims about what the change does, read against the diff | Every claim the description makes about what was changed is visible in the diff, and the description does not claim the plan was followed in full when the diff does less or more. Fails when the description says something was done (a docs update, a test, a second fix) that the diff does not contain, or omits a substantive change the diff does make | required |

It reads this way because silent drift has two halves: the diff can do more or less than the plan,
and the description can claim fidelity the diff contradicts (pkg-17 in the set: the plan promises a
dev warning and a docs update, the diff contains only the warning, and the description claims both).
I considered folding both into "Diff matches plan" and rejected that, because a PR can match its
plan exactly and still describe itself wrongly to a reviewer, and the reviewer only sees the
description. Keeping it as its own check means the evidence line names the specific false claim. I
also wrote into it that a description which honestly says something was left out passes, so a
disclosed shortfall (pkg-13, pkg-16) is not punished.

**Trade-offs**

The check reads each description claim against the diff, so it can fail a correct PR over loose
wording: pkg-11's "One function touched" next to a diff that adds a helper is the case I accept it
will sometimes miss, and it did in the committed run. Making the check ignore approximate wording
would let through descriptions like pkg-17's, which claim work that is not in the diff. I did not
change the check after the committed run, so no canary runs were needed; the evidence that nothing
else is affected is that both full runs agreed with the gold label on every other package, including
both standards-wall and all four silent-drift packages.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
