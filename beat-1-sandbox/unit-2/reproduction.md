# Unit 2 — Claim and Reproduce

Path: `beat-1-sandbox/unit-2/reproduction.md`

Record of your claim and reproduction on the issue you chose in Unit 1, and of the
evaluation runs that produced `eval-run.txt`. This file is graded at the path above; a copy
kept anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your identity upstream

**GitHub username**

himavanthkar

---

## Posted upstream

**Claim comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56#issuecomment-5851960934

```
Hi! I'd like to take this one on as my first contribution. I'll reproduce the issue's minimal example (a headingless document run through StructuralChunker.chunk()) and trace where it gets silently dropped in _extract_sections(), then post a repro report with what I find before opening a PR.
```

**Reproduction comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56#issuecomment-5851968134

```
Environment: Python 3.11.15 (sandbox) / 3.13 (confirmed on my own machine), tiktoken 0.14.0 (real library, no substitution needed), repo: himavanthkar/pathreview-ai301-fa26-s1 (fork of codepath/pathreview-ai301-fa26-s1) @ f89c06f, macOS (arm64) + Linux (x86_64) — reproduced on both.

Steps: Ran the issue's exact minimal example:

from ingestion.chunking.structural_chunker import StructuralChunker
c = StructuralChunker()
print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))

Output:
$ python3 -c "..."
0

Matches the issue exactly: 0 chunks for a ~1000-char document with no markdown headings.

Root cause, traced through ingestion/chunking/structural_chunker.py, _extract_sections():
- Content lines are only appended to current_section_lines when heading_stack or current_section_lines is true (line 120). For a document with zero headings, heading_stack never gets populated, so this condition is always false — every content line is silently discarded.
- At the end of the method, the final section is only saved when current_section_lines and heading_stack (line 124). Since heading_stack is empty for a headingless document, this is also false, so nothing is emitted even if content had been collected.
- Net effect: _extract_sections() returns [], so chunk()'s for section in sections loop (line 42) never executes, and chunk() returns [].

The existing test test_document_with_no_headings in tests/unit/test_structural_chunker.py already encodes the expected fix (assert len(result) >= 1) and is marked @pytest.mark.xfail(strict=True, reason="issue #56: structural chunker drops documents with no headings"), matching the seeded-defect note in docs/CONTRIBUTING.md.

Expected: A document with no headings should still be chunked — as a single block, or via a fallback strategy (the issue leaves this choice open).

Actual: chunk() returns an empty list; the document is silently excluded from the RAG index rather than chunked at all.
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

First run (full, 20 packages): 16/20. Four clear-accept packages failed: pkg-05, pkg-09, pkg-10, pkg-12.

Targeted re-run (`--only pkg-05,pkg-09,pkg-10,pkg-12` plus canaries `pkg-02,pkg-08,pkg-13,pkg-20`) after revising two checks: 8/8 agreement on those 8 packages.

Confirming full run (final): agreement: 20/20 scored items (bar: 18/20: PASS), categories: clear-accept 8/8 disclosure 1/1 no-evidence 4/4 unfollowable-comms 3/3 wrong-target 4/4.

**Package analysis**

pkg-09: gold label `accept`. My rubric's first version decided `reject`, failing on my "Behavior matches issue" check. My final rubric decides `accept`, matching gold.

pkg-09 is an honest cannot-reproduce report: the student tried to trigger fd's `--exec-batch` argument-size reordering bug, ran a real, detailed attempt (120,000 files, five repeated runs, a padding variation), and reported that they could not provoke the reordering, explaining exactly what differed from the original report's conditions. My first "Behavior matches issue" check read literally — "the artifact demonstrates the same behavior the issue describes" — and an honest non-reproduction artifact by definition does not show the issue's behavior, so it failed every cannot-reproduce package on this check even though the report was a model of honesty. I revised the check to explicitly pass when the report claims it could NOT reproduce the issue (pushing that judgment to the "Honest outcome" check instead, which is where a fabricated or unsupported cannot-reproduce claim should actually be caught).

**Check rationale**

"Behavior matches issue | The artifact (output excerpt, log, screenshot) read against the issue's described behavior | If the report claims reproduction: the artifact demonstrates the SAME specific behavior the issue describes (same trigger condition, same error/output shape), not merely that the tool ran, and not an adjacent or different-cause symptom. If the report honestly claims it could NOT reproduce the issue: this check passes (judge the honesty of a cannot-reproduce claim under Honest outcome instead, not here) | required"

It reads this way because my first draft conflated two different questions under one check: "does the evidence match the issue" and "is the report honest." Those need to be separate, because an honest cannot-reproduce report has no matching evidence by design — that is what makes it honest — so grading it on evidence-match alone always fails it. Splitting the questions let "Behavior matches issue" stay strict for reproduction claims (catching pkg-02, pkg-08, pkg-16, pkg-17 — all wrong-target packages where the artifact shows a different bug) while "Honest outcome" independently catches a fabricated or evidence-free cannot-reproduce claim.

**Trade-offs**

The revised check trades some precision for correctly handling honest failure reports: by passing every cannot-reproduce claim through this specific check, a report that *falsely* claims it could not reproduce something it never actually attempted would also pass "Behavior matches issue" — that miss is caught by "Honest outcome" instead, which requires a real, evidenced attempt behind any cannot-reproduce claim. I confirmed this split doesn't let anything slip through by re-running canaries from every other single-package-sensitive category after the change (pkg-02, pkg-08 for wrong-target; pkg-13 for no-evidence; pkg-20 for disclosure) — all four still correctly rejected, so the loosened check didn't buy back a false accept anywhere else.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/repro-check/`.
