# Procedure: how this skill grades a plan package

## Read order

1. Read the issue section first (title, body, labels) and note: what
   behavior is reported as wrong, and what the reporter or a maintainer
   says the expected behavior should be.
2. Read the repo-facts block next and note: the repo's stated AI
   disclosure policy (required, permissive, or none), and any
   bug-report/contribution template requirements.
3. Read the thread highlights next, in chronological order, and note
   any comment from a maintainer (OWNER, MEMBER, or COLLABORATOR
   association) that names a specific cause, a specific file/function,
   or explicitly rejects an approach. These are the "explicit
   direction" facts the Comment check needs.
4. Read the repro evidence block next, in order, and note: every step's
   command and observed output, and separately, every control run and
   its result. Control runs are read with the same weight as the
   failing run — a diagnosis that fits the failing run but contradicts
   a control run is still wrong.
5. Read the candidate plan last, section by section in the order it
   presents them (diagnosis, scope, files, approach, test plan,
   risks/unknowns).
6. Read the candidate plan comment last of all, after you already know
   what the thread and the repo policy require, so you are reading it
   as a maintainer on the thread would.

Read the issue and repro evidence before the plan so the grading
checks evidence the plan should have followed, not evidence the plan
asserts about itself.

## Evidence gathering

- **Diagnosis grounded in evidence**: pull the plan's stated cause
  (usually under a "Diagnosis" heading). Pull every result recorded in
  the repro evidence (the failing run's output, and every control
  run's output). Record each control run's result next to what the
  plan's diagnosis would predict for it.
- **Bounded scope**: pull the plan's "Scope", "Files", and "Approach"
  sections (or their equivalent) in full. Record every file or
  component named, and any line stating what is explicitly out of
  scope.
- **Concrete, startable approach**: reuse the files/approach text
  gathered above. Record whether a single approach is named, or
  whether multiple unresolved options are presented as alternatives.
- **Observable test plan**: pull the plan's "Test plan" section.
  Record the exact command or action named and the exact expected
  result stated for it.
- **Honest unknowns**: pull any "Risks", "Unknowns", or similar section.
  Record each stated uncertainty, and separately check whether that
  same uncertain thing is asserted as settled fact elsewhere in the
  plan (a contradiction between the two is the thing this check looks
  for).
- **Comment respects thread and disclosure**: pull the candidate plan
  comment in full. Compare it against the maintainer-direction facts
  recorded during Read order step 3, and against the AI-policy fact
  recorded during Read order step 2.

## Check execution

1. Grade checks in the table's row order.
2. Grade "Diagnosis grounded in evidence" first, since the remaining
   checks assume the diagnosis is the one being evaluated; if it fails,
   still grade every other check independently (do not early-exit) so
   the output shows the package's full failure picture.
3. Evidence for a check is genuinely absent only when the package
   contains no relevant section at all (for example, no test plan
   section exists anywhere in the candidate plan). In that case grade
   the check `fail` with evidence "no test plan section present" (or
   the equivalent), not `unclear` — an absent section is a known fact,
   not an open question.
4. Grade `unclear` only when a section exists but its content does not
   let you tell pass from fail (for example, a diagnosis section that
   names no cause at all, just a description of symptoms).
5. Do not re-read the whole package for each check; grade all six
   checks from the facts already recorded during Evidence gathering.
   Re-read a specific section only if a check's evidence note turns out
   to be incomplete.

## Verdict assembly

1. Apply the rubric's verdict rule: accept only if every required check
   passed; any required fail (including any check graded `unclear`,
   which counts as fail) produces reject.
2. In the output JSON, each check's `evidence` field quotes or names
   the specific fact that decided it (a quoted line from the plan, a
   named control run result, a named thread comment) — never a
   restatement like "looks fine" or "plan is bounded."
3. When the verdict is reject, the readable summary before the JSON
   names every failed check, not just the first one, so the student can
   fix all of them in one revision pass.
