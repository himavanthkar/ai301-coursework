# Evidence guide: where proof lives in a plan package

## Diagnosis vs. repro evidence

Where it lives: the candidate plan's "Diagnosis" section (what the plan
says is the root cause), read against every line of the "## Repro
evidence" block — both the failing run's output and every control run.

What good looks like: the stated cause explains the failing run AND is
consistent with what every control run shows. A control run is
evidence with the same weight as the failing run — if a control run
(same input, one variable removed) still fails, or an unrelated-cause
control still succeeds, a diagnosis that doesn't account for that
result is contradicted by the package's own evidence, even if it
sounds plausible on its own. Watch especially for a diagnosis that
explains the surface symptom (an error message, a version difference)
but ignores a deeper step in the repro (a `--debug` trace, a stack
frame, a control isolating one variable) that points somewhere else.

## Scope and files

Where it lives: the plan's "Scope," "Files," and "Approach" sections
(or their equivalent headings).

What good looks like: every named file is one a reader could connect
directly to the diagnosed cause. An explicit "not in scope" line names
what the plan is deliberately NOT doing (a nearby refactor, a related
but separate bug, a broader redesign) — its presence is a good sign,
not a requirement by itself. Scope creep shows up as: a one-line fix
diagnosis paired with a multi-file, multi-system change list; language
like "while I'm in there" or "and also clean up"; or a file list that
includes components the diagnosis never mentions.

## Approach concreteness

Where it lives: the same "Approach" / "Files" text as above, read for
decisiveness rather than content.

What good looks like: one chosen path, named specifically enough that
someone unfamiliar with the issue could start immediately — either a
specific file, or a specific module/path paired with a concrete
narrowing move already done (a working debug trace pointing at the
leak's origin, a line number from the repro's own evidence, a function
the control runs implicate), with exact function names left to pin
down during the build. Fails look like: two or more options presented
without a choice between them ("either refactor X or patch Y"), a
location described only generally with no narrowing evidence
("somewhere in the parser," "the config layer"), or a deferral
disguised as a plan ("I'll figure out the right approach once I
start").

## Test plan

Where it lives: the plan's "Test plan" section.

What good looks like: a specific command or action to run, paired with
a specific, observable expected result — an exact output, an exit
code, a specific line of behavior that changes. A test plan built from
the same repro steps posted in week 2 (or the house repro pack) is a
strong sign, since it proves the fix against the same evidence that
proved the bug. Fails look like: a vague expectation ("should work
correctly now," "should feel faster," "no more crashes" with no
specific command named), or no test plan section at all.

## Risks and unknowns

Where it lives: the plan's "Risks," "Unknowns," or similarly-headed
section, read against claims made elsewhere in the plan (diagnosis,
approach, scope).

What good looks like: a genuinely uncertain thing (an untested
assumption, a possible wider pattern not yet checked, a performance
cost not yet measured, a platform the student can't test) is named as
uncertain somewhere in the plan — in a dedicated Risks/Unknowns
section, or inline in the approach/scope text — with what the student
would do if it turns out to matter. Naming a planned mitigation for a
flagged risk ("I will bound the drain to X, and call that out for
review") is still an honest flag, not a fail: the uncertainty is still
named as unresolved. A fail looks like: an unknown that is asserted as
settled fact elsewhere in the plan (a real contradiction, not just a
mitigation), or a plan with real unaddressed uncertainty (an
assumption about the trigger condition, an alternate code path never
ruled out, an untestable platform never mentioned) that states no
risks anywhere. A plan with no genuine uncertainty at all needs no
risks section to pass this check.

## Comments vs. thread and policy

Where it lives: the candidate plan comment, read against two sources —
the "Thread highlights" section (for explicit maintainer direction:
look for OWNER/MEMBER/COLLABORATOR comments naming a cause, a file, or
rejecting an approach) and the repo-facts block's stated contribution/
AI policy (for a disclosure requirement).

What good looks like: when a maintainer already named a cause, a file,
or ruled an approach out in the thread, the comment's plan follows or
explicitly engages with that direction rather than silently proposing
something the maintainer already addressed or rejected. When the
repo's policy requires disclosing AI assistance (naming the tool and
the extent of help), the comment states that plainly — silence is a
fail whenever the policy asks for it, regardless of how good the plan
itself is. When the repo has no stated AI policy, this half of the
check passes automatically.
