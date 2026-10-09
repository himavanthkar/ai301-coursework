# Rubric: is this plan ready to post and build from?

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| Diagnosis grounded in evidence | The plan's stated diagnosis/cause, read against every step and control run in the repro evidence block | The diagnosis is consistent with all of the repro evidence's observed results, including control runs; it does not contradict or silently ignore a result that points at a different cause | required |
| Bounded scope | The plan's scope/approach/files sections | The described change stays limited to what the issue's defect needs; named files are directly relevant to the fix; the plan explicitly excludes any refactor, migration, or redesign beyond the one fix, rather than bundling extra work in | required |
| Concrete, startable approach | The plan's approach and files sections | Names a specific file or module and one chosen approach a stranger could start executing immediately; a general area is acceptable when paired with a concrete narrowing move already done (a working debug trace, a line number, a specific function the evidence points to), with exact function names left to be pinned down during the build. Fails on open-ended investigation ("look into X or Y"), unresolved alternatives ("whichever is easier"), or a location named only vaguely with no narrowing evidence ("somewhere in the parser") | required |
| Observable test plan | The plan's test plan section | Names the exact command/action to re-run and the exact observable outcome expected (a specific output, exit code, or behavior); fails on a vague outcome ("should work better", "should feel faster") or no test plan at all | required |
| Honest unknowns | Any genuine uncertainty in the plan (a dedicated Risks/Unknowns section, or uncertainty stated inline elsewhere), read against the rest of the plan's claims | Every genuine uncertainty the plan has (an unverified assumption, an untested case, a judgment call) is flagged as uncertain somewhere in the plan, not asserted as settled fact; stating a planned mitigation or fallback for a flagged risk does not undo the flag. A plan with no real uncertainty anywhere needs no risks section to pass | required |
| Comment respects thread and disclosure | The candidate plan comment, read against the thread highlights and the repo's stated AI policy | The comment acknowledges and does not contradict or ignore explicit direction a maintainer already gave in the thread (a named cause, a named file, a rejected approach); and, when the repo's stated policy requires disclosing AI assistance, the comment discloses it (tool + extent) | required |

## Verdict rule

Accept if all required checks pass. Reject if any required check fails. Unclear counts as fail. There are no preferred checks this week; every failure family the lecture named gates the verdict.
