# Procedure: how this tool grades a PR package

## Read order

1. Read the issue first (eval: the "Issue" section; live: the issue body). Note the trigger
   condition: the exact input, command, or state that produces the bug, and the expected behavior.
2. Read the repo's stated asks next (eval: the repo-facts block's "pull requests" and "contribution
   policy" lines; live: `.github/PULL_REQUEST_TEMPLATE.md` and `docs/CONTRIBUTING.md`). Write down
   each required template section or checklist item, and whether the policy requires AI-use
   disclosure.
3. Read the thread (eval: "Thread highlights"; live: the issue's comments). Note any maintainer
   direction that the plan or PR is supposed to follow.
4. Read the plan (eval: "Plan context"; live: `plan.md`). Write down three lists: the changes it
   promises (file by file), what it says is out of scope, and its test plan (each command and its
   expected after-result). Read its deviation notes last and record each one as an amendment to
   those lists.
5. Read the PR in this order: the diff (and the commit list), then the test evidence, then the
   title and description. Reading the diff before the description means the diff is judged on what
   it does, not on what the description says it does.

## Evidence gathering

- **Diff matches plan**: list every file the diff changes and, for each hunk, one line saying what
  it does. Place each hunk against the plan's promised changes and deviation notes: matched, or
  unmatched. Then go through the plan's promised changes: present in the diff, or missing. For each
  missing one, look in the deviation notes and the description for a sentence saying it was left
  out on purpose.
- **Description matches diff**: list each claim the title and description make about what changed
  ("adds X", "updates the docs", "also handles Y", "per the plan"). Next to each, write the hunk that
  shows it, or "not in diff". Also note any substantive hunk the description never mentions.
- **Decisive test evidence**: from the test evidence (and the description's Testing section), copy
  the commands run and their outputs. For each, record: does it exercise the issue's trigger
  condition from Read order step 1, or only a control or an unchanged path? Is there a before and an
  after, each with observable output? Is at least one test or check command named with its result?
- **Clean diff**: scan every added and removed line for debug output, commented-out code, lines that
  change only formatting or whitespace, and hunks that do not belong to the fix. Record each one
  with its file.
- **Repo standards met**: for each required section or checklist item from Read order step 2,
  record whether the description contains it with real content. If disclosure is required, quote
  the description's disclosure sentence, or record that there is none.

## Check execution

1. Grade the checks in the rubric's table order, each from the evidence recorded above. Re-read a
   part of the package only if a recorded note turns out to be incomplete.
2. Grade every check even after one fails, so the summary shows the full picture.
3. Apply each check's pass condition exactly as written. A hunk or omission that a deviation note or
   the description discloses is matched, not drift.
4. Grade `fail` when the evidence a check needs is absent from the package (for example, no test
   evidence at all, or no description section the template requires), since an absent artifact is a
   known fact. Grade `unclear` only when the evidence exists but does not let you decide (for
   example, a diff truncated mid-hunk).
5. For each check, write one evidence line that quotes or names the deciding fact: the hunk, the
   claim, the command and its output, or the missing section.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if all five checks pass; any `fail` or `unclear`
   produces reject.
2. In the readable summary, name every failed or unclear check, not just the first one.
3. Emit the JSON block with one entry per check, in table order, each with its evidence line, and
   the verdict. Nothing follows the block.
