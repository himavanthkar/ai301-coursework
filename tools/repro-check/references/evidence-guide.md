# Evidence guide: where proof lives in a reproduction package

## Environment

Where it lives: the repro report's "Environment:" line or section (eval
bundle: under "## Candidate repro report"; live: the student's draft repro
comment). Compare it against the issue's own stated target version/platform
(eval bundle: the "## Issue" section and "Repo facts"; live: the issue body
and its repo-facts equivalent gathered via `gh`/the web).

What good looks like: the tool's version is named explicitly (not "latest"),
the OS/platform is named, and any dependency versions the issue calls out by
name (a library, a runtime, a driver) are also recorded. If the environment
differs from the issue's target version, the report says so explicitly
rather than staying silent about it.

## Steps

Where it lives: the repro report's numbered steps or command transcript.

What good looks like: every command, config file, or starting state needed
is either shown in full inside the report, or the report explicitly says
it ran the issue's own script/reproduction verbatim and names the exact
parameters used (in which case the reader can pull the script from the
issue itself). A stranger with a clean machine and the same tool version
could reach the same starting state either way. This check fails only when
steps are missing outright, a required piece (e.g. a platform-specific
driver argument) is silently skipped, or the repro depends on
infrastructure the reader cannot reach (a private, unshared monorepo or
config) with nothing usable given in its place.

## Behavior shown

Where it lives: the artifact block in the repro report (command output, log
excerpt, screenshot description) read side by side with the issue's
"what happened" / expected-vs-actual description.

What good looks like: when the report claims reproduction, the artifact's
output shows the same trigger condition and the same resulting behavior
the issue names — same error type, same exit code family, same observable
symptom — not just that the tool ran without crashing, and not a different
bug that merely looks similar. Check the specifics: if the issue reports a
crash with a specific error, an artifact showing a different, milder error
(e.g. a graceful validation message instead of a panic) is a different
target, not a confirmation. If the issue names an exact input value or
flag syntax, the report's commands must use that same syntax, not an
approximation. When the report instead honestly claims it could NOT
reproduce the issue, this check is not the one to fail it on — grade the
genuineness of that attempt under Honesty below instead.

## Honesty

Where it lives: the report's own stated conclusion ("Expected/Actual" or
closing sentence) compared against the artifacts shown a few lines above
it.

What good looks like: the words claim exactly what the artifacts prove, no
more. "I confirmed X" needs an artifact that actually shows X. An honest
"I could not reproduce this; here is what I tried and what I saw instead"
is a full pass when the attempt is real and evidenced. A confident,
narrated success ("exactly as the issue describes") sitting next to an
artifact that shows something else (wrong error, wrong exit code, or no
real output at all — just a version banner or a "ran successfully" claim)
fails this check regardless of how detailed the surrounding prose is.

## Comms

Where it lives: the candidate claim comment, read against the repo's
stated bug-report template asks and its contribution/AI policy (eval
bundle: "Repo facts"; live: the repo's CONTRIBUTING.md / AI policy file
and the issue's own template, gathered via `gh`/the web).

What good looks like: the claim names the specific issue and what the
student found or intends to check next (not a template greeting applied to
any issue). It never promises a fix, a timeline, or demands exclusive
reservation of the issue. Where the repo's policy requires disclosing AI
assistance (naming the tool and the extent of help), the comment or report
text contains that disclosure explicitly — silence on AI use is a fail
whenever the policy asks for it, even if every other check passes.
