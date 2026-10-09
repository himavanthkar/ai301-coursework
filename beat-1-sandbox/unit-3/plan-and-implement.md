# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

himavanthkar

**Plan comment**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56#issuecomment-6073224229

~~~
Plan for this one, building on my repro above (0 chunks for the issue's 1000-char heading-less document on `f89c06f`):

**Cause:** in `StructuralChunker._extract_sections()`, content lines are only kept once a heading has been seen (line 120), and the final section is only saved when `heading_stack` is non-empty (line 124). A document with no headings therefore produces no sections, and `chunk()` returns `[]`.

**Change:** one fallback in `_extract_sections()`: if no heading was seen and the text is non-empty, treat the whole text as a single section (empty heading path, level 0). `chunk()` then handles it like any other section, so documents over 800 tokens still go through semantic sub-chunking. I'll also remove the `xfail` marker on `test_document_with_no_headings`, per CONTRIBUTING.

**Not changing:** text before the first heading in documents that *do* have headings (also dropped today, but a separate case I'll flag in the PR), the `# noqa` seeded line, or `SemanticChunker`.

**Test plan:** re-run my repro command and expect `1` instead of `0`; `test_document_with_no_headings` should go from XFAIL to PASSED with the other 14 chunker tests unchanged; a headed control document should still give heading paths `A` and `A > B`.

**Open question:** the issue leaves "single block vs. another strategy" open. I went with single block since it reuses the existing large-section path; happy to adjust if a different fallback is preferred.
~~~

---

## Your branch

**Branch**

fix/56-structural-chunker-no-headings

**Evidence**

My Unit 2 repro command (plus the headed-document control from my test plan and the chunker
test file), run on my Mac with the real tiktoken 0.14.0, Python 3.13.

Before the fix (branch created from `f89c06f`, no code changes yet):

~~~
$ (echo '$ repro'; python3 -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"; echo '$ headed control'; python3 -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print([x.metadata['heading_path'] for x in c.chunk('# A\nbody a\n## B\nbody b', {})])"; echo '$ python3 -m pytest tests/unit/test_structural_chunker.py -v'; python3 -m pytest tests/unit/test_structural_chunker.py -v) 2>&1 | tee ../evidence-before.txt
$ repro
0
$ headed control
['A', 'A > B']
$ python3 -m pytest tests/unit/test_structural_chunker.py -v
============================= test session starts ==============================
platform darwin -- Python 3.13.0, pytest-9.0.2, pluggy-1.6.0 -- /usr/local/bin/python3
cachedir: .pytest_cache
rootdir: /Users/abhil/Desktop/all codepath ai capstone projects/pathreview-ai301-fa26-s1
configfile: pyproject.toml
plugins: anyio-4.9.0, asyncio-1.3.0
asyncio: mode=Mode.STRICT, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 15 items

tests/unit/test_structural_chunker.py::TestStructuralChunker::test_empty_input_returns_empty_list PASSED [  6%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_whitespace_only_input PASSED [ 13%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings XFAIL [ 20%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_nested_headings PASSED [ 26%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_path_format PASSED [ 33%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_large_section_sub_chunked PASSED [ 40%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunk_metadata_includes_heading_level PASSED [ 46%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunk_metadata_structure PASSED [ 53%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_preserve_source_metadata PASSED [ 60%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_multiple_h1_headings PASSED [ 66%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_path_breadcrumb PASSED [ 73%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunks_have_text_content PASSED [ 80%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_section_extraction_with_multiple_levels PASSED [ 86%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_not_in_middle_of_content PASSED [ 93%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_empty_sections_handled PASSED [100%]

======================== 14 passed, 1 xfailed in 0.06s =========================
~~~

After the fix (commit `3c472a9` on `fix/56-structural-chunker-no-headings`):

~~~
$ (echo '$ repro'; python3 -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"; echo '$ headed control'; python3 -c "from ingestion.chunking.structural_chunker import StructuralChunker; c = StructuralChunker(); print([x.metadata['heading_path'] for x in c.chunk('# A\nbody a\n## B\nbody b', {})])"; echo '$ python3 -m pytest tests/unit/test_structural_chunker.py -v'; python3 -m pytest tests/unit/test_structural_chunker.py -v) 2>&1 | tee ../evidence-after.txt
$ repro
1
$ headed control
['A', 'A > B']
$ python3 -m pytest tests/unit/test_structural_chunker.py -v
============================= test session starts ==============================
platform darwin -- Python 3.13.0, pytest-9.0.2, pluggy-1.6.0 -- /usr/local/bin/python3
cachedir: .pytest_cache
rootdir: /Users/abhil/Desktop/all codepath ai capstone projects/pathreview-ai301-fa26-s1
configfile: pyproject.toml
plugins: anyio-4.9.0, asyncio-1.3.0
asyncio: mode=Mode.STRICT, debug=False, asyncio_default_fixture_loop_scope=None, asyncio_default_test_loop_scope=function
collecting ... collected 15 items

tests/unit/test_structural_chunker.py::TestStructuralChunker::test_empty_input_returns_empty_list PASSED [  6%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_whitespace_only_input PASSED [ 13%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_no_headings PASSED [ 20%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_document_with_nested_headings PASSED [ 26%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_path_format PASSED [ 33%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_large_section_sub_chunked PASSED [ 40%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunk_metadata_includes_heading_level PASSED [ 46%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunk_metadata_structure PASSED [ 53%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_preserve_source_metadata PASSED [ 60%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_multiple_h1_headings PASSED [ 66%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_path_breadcrumb PASSED [ 73%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_chunks_have_text_content PASSED [ 80%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_section_extraction_with_multiple_levels PASSED [ 86%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_heading_not_in_middle_of_content PASSED [ 93%]
tests/unit/test_structural_chunker.py::TestStructuralChunker::test_empty_sections_handled PASSED [100%]

============================== 15 passed in 0.06s ==============================
~~~

Full unit suite on the same machine, after creating the project venv
(`python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"`):

~~~
$ make test-unit                       # with the fix
================== 376 passed, 52 xfailed, 1 warning in 9.52s ==================
$ git stash && make test-unit          # without the fix
================== 375 passed, 53 xfailed, 1 warning in 4.66s ==================
$ make lint
All checks passed!
$ make typecheck
Success: no issues found in 76 source files
~~~

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

1. Full run: 18/20 (bar PASS). Disagreements: pkg-03 (failed: Honest unknowns) and pkg-14
   (failed: Concrete, startable approach, Honest unknowns), both gold accept.
2. Partial run after loosening two checks, `--only pkg-03,pkg-14,pkg-10,pkg-17,pkg-18`
   (pkg-10/17/18 as unbuildable-category canaries): 5/5. Partial, so no bar verdict.
3. Full run: 20/20 (bar PASS), every category matched.
4. Full confirming run with `--save-run eval-run.txt`: 19/20 (bar PASS), categories
   clear-accept 6/7, scope-creep 4/4, thread-convention 2/2, unbuildable 3/3, wrong-cause 4/4.
   This is the run in `eval-run.txt`: "agreement: 19/20 scored items  (bar: 18/20: PASS)".

**Package analysis**

pkg-14 (zellij reattach leak). Gold label: accept. My rubric's final saved run decided reject,
failing "Honest unknowns"; in run 1 it also rejected (failing "Concrete, startable approach" and
"Honest unknowns"), and in run 3 it accepted.

The plan names its files only as a module area: "the client attach/reattach path in
`zellij-server` (session connection handling) and `zellij-client`'s terminal query issuance;
exact functions to be pinned in the PR after tracing the query issuance with debug logs, which I
have working". My first concrete-approach check wanted a named file, so it read "exact functions
to be pinned" as an unchosen location. I loosened that check to accept a module area when a
narrowing move is already done (here, the working `zellij --debug` trace), which fixed it.

The remaining disagreement is on honest unknowns. The plan states a risk ("draining input at
attach risks eating one legitimate keystroke") and then a mitigation ("I will bound the drain to
OSC response patterns rather than a time window, and call that decision out for review"). My
grader sometimes reads the mitigation as settling the risk, even though the plan keeps it open for
review. I added "stating a planned mitigation or fallback for a flagged risk does not undo the
flag" to the pass condition, which made it agree in run 3, but the saved run still flipped it. So
the wording reduces the problem without removing it: this package sits right at the boundary
between "risk flagged with a plan" and "risk declared solved".

**Check rationale**

| Honest unknowns | Any genuine uncertainty in the plan (a dedicated Risks/Unknowns section, or uncertainty stated inline elsewhere), read against the rest of the plan's claims | Every genuine uncertainty the plan has (an unverified assumption, an untested case, a judgment call) is flagged as uncertain somewhere in the plan, not asserted as settled fact; stating a planned mitigation or fallback for a flagged risk does not undo the flag. A plan with no real uncertainty anywhere needs no risks section to pass | required |

It reads this way because the first version only looked at "the plan's stated risks/unknowns"
and required uncertainties to be "flagged as open questions". That made a dedicated Risks
section the evidence source, so pkg-03 failed: its one real uncertainty (whether bzip2, lz4 and
brotli accept `--`, "I have verified gzip, xz, and zstd locally and will verify the remaining
three") is stated inline in the Approach, not under a Risks heading. I widened the evidence to
uncertainty "stated inline elsewhere", added that a mitigation does not undo a flag (for pkg-14),
and added that a plan with no real uncertainty needs no risks section, so the check grades
whether uncertainty is hidden, not whether a section exists.

**Trade-offs**

Loosening the check means a plan can pass with uncertainty buried in one inline clause that a
maintainer might skim past; the check no longer forces risks into a place readers look. I also
accept that it still misses pkg-14 some of the time (it did in the saved run), because the line
between a mitigated risk and a settled one is a judgment the grader makes differently on
different runs. To make sure the loosening didn't let real failures through, I re-ran the
unbuildable packages pkg-10, pkg-17 and pkg-18 as canaries with `--only` (they still rejected),
and the next full run kept wrong-cause 4/4 and scope-creep 4/4, so no reject package flipped to
accept.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
