# Rubric: is this reproduction package ready to post?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Environment recorded | Repro report's environment line/section | States the tool's version, OS/platform, and any other dependency versions relevant to the issue's stated target (e.g. language runtime, library versions named in the issue) | required |
| Steps followable | Repro report's steps section | A stranger could re-run: either the full commands/config are shown inline, OR the report explicitly says it ran the issue's own reproduction steps/script verbatim and names the specific parameters used. Fails only when steps are missing entirely, or require infrastructure the reader cannot reach (e.g. a private, unshared repo/config) | required |
| Behavior matches issue | The artifact (output excerpt, log, screenshot) read against the issue's described behavior | If the report claims reproduction: the artifact demonstrates the SAME specific behavior the issue describes (same trigger condition, same error/output shape), not merely that the tool ran, and not an adjacent or different-cause symptom. If the report honestly claims it could NOT reproduce the issue: this check passes (judge the honesty of a cannot-reproduce claim under Honest outcome instead, not here) | required |
| Honest outcome | The report's stated conclusion compared against what its own artifacts show | The claimed outcome (reproduced / cannot reproduce) is exactly what the shown artifacts support. An honest, evidenced cannot-reproduce (a real attempt is described, with what was tried and what differed) passes. A confident claim unsupported by, or contradicted by, the artifact fails, as does a cannot-reproduce claim with no real attempt shown | required |
| AI disclosure compliance | Repo facts' contribution/AI policy section, checked against the claim comment and repro report text | If the repo's stated policy requires disclosing AI assistance, the comment(s) disclose it (tool + extent); if the repo has no such requirement, this check passes automatically | required |
| Claim comment respects conventions | Candidate claim comment | Comment is specific to this issue (names what was found/will be checked), not generic boilerplate, and does not over-promise (no guaranteed timelines, no demanding the issue be reserved) | required |

## Verdict rule

Accept if all required checks pass. Reject if any required check fails. Unclear counts as fail. There are no preferred checks this week; every family the lecture named gates the verdict.
