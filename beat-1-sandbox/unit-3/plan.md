# Plan: issue #56 — Structural chunker silently drops documents that contain no headings

## Diagnosis

From my repro (https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56#issuecomment-5851968134),
run against `f89c06f` with the real tiktoken 0.14.0 on macOS (arm64):

```bash
python3 -c "from ingestion.chunking.structural_chunker import StructuralChunker
c = StructuralChunker()
print(len(c.chunk('This is a plain document with no headings at all. ' * 20, {})))"
```

Output:

```
0
```

0 chunks for a ~1000-char document with no markdown headings, matching the issue.

The cause is in `StructuralChunker._extract_sections()` (`ingestion/chunking/structural_chunker.py`):

- Line 120: a content line is only kept when `heading_stack or current_section_lines` is true. With no
  heading ever seen, both are empty, so every line is discarded.
- Line 124: the final section is only saved when `current_section_lines and heading_stack`, which is
  also false for a heading-less document.

So `_extract_sections()` returns `[]`, the `for section in sections` loop in `chunk()` (line 42) never
runs, and `chunk()` returns `[]`. The existing test `test_document_with_no_headings` encodes the expected
result (`assert len(result) >= 1`) and is marked `@pytest.mark.xfail(strict=True, reason="issue #56: ...")`.

## Scope

In scope: when a document contains no markdown headings at all, treat its whole text as one section
(empty heading path, level 0). `chunk()` then handles it like any other section: one chunk if it is
within `SECTION_TOKEN_LIMIT` (800 tokens), or semantic sub-chunks if it is larger. This is the
"chunked as a single block" option the issue names.

Out of scope:
- Text that appears before the first heading in a document that does have headings. It is also dropped
  today (same line-120 condition), but it is a different case from this issue; I will mention it in the
  PR as a possible follow-up rather than change it here.
- The `current_level = heading_level  # noqa: F841` line. `docs/CONTRIBUTING.md` asks that `# noqa`
  seeded-defect lines not be cleaned up, so I leave it alone.
- Any change to `SemanticChunker`, `StrategySelector`, or chunk metadata keys.

## Files

- `ingestion/chunking/structural_chunker.py`: in `_extract_sections()`, just before `return sections`,
  add a fallback: if `heading_stack` is empty and `text.strip()` is non-empty, append
  `{"content": text.strip(), "path": [], "level": 0}`.
- `tests/unit/test_structural_chunker.py`: delete the `@pytest.mark.xfail(strict=True, ...)` decorator on
  `test_document_with_no_headings`, as `docs/CONTRIBUTING.md` requires when fixing a seeded bug.

## Approach

1. Create branch `fix/56-structural-chunker-no-headings` on my fork.
2. Add the fallback in `_extract_sections()` (one `if` block, three lines).
3. Remove the `xfail` decorator from `test_document_with_no_headings`.
4. Run the test plan below, then `make lint`, `make typecheck`, `make test-unit`.

## Test plan

1. Re-run my repro command from the issue thread (same command as in Diagnosis).
   - Before (posted repro): prints `0`.
   - Expected after the fix: prints `1`.
2. `python3 -m pytest tests/unit/test_structural_chunker.py -v`
   - Before: `test_document_with_no_headings XFAIL`, the other 14 pass.
   - Expected after: `test_document_with_no_headings PASSED`, the other 14 still pass (no `XPASS(strict)` error,
     because the marker is removed).
3. Control, headed document unchanged:
   `c.chunk('# A\nbody a\n## B\nbody b', {})` should still return 2 chunks with heading paths `A` and `A > B`,
   same as before the change.
4. `make test-unit` passes with no new failures.

## Risks and unknowns

- Heading-less chunks get `heading_path == ""` and `heading_level == 0`. A grep for `heading_path` and
  `heading_level` across the repo (`.py`, `.ts`, `.tsx`) finds no use outside `ingestion/chunking/` and
  `tests/`, so nothing downstream reads these keys today. Not verified: whether stored chunks from a
  previously indexed heading-less document need re-ingestion (they were never created, so I expect not).
- The issue leaves open whether a heading-less document should be one block or use another strategy.
  I chose the one-block fallback because it reuses the existing large-section path (semantic sub-chunking
  above 800 tokens). If maintainers prefer a different strategy, the change is confined to one `if` block.
- Preamble text before the first heading in headed documents stays dropped (see Scope); this fix does not
  claim to address it.

## Deviations

The code change matches the plan: one fallback block in `_extract_sections()` and the `xfail` decorator removed, nothing else touched. Test-plan steps 1-3 came out exactly as predicted on my Mac (repro `0` -> `1`, headed control `['A', 'A > B']` unchanged, chunker tests 14 passed + 1 xfailed -> 15 passed).

What differed is how I got to the checks in Approach step 4. `make setup` also runs database migrations and seed data that need Docker, so I skipped it and created the venv by hand (`python3 -m venv .venv && .venv/bin/pip install -e ".[dev]"`), which is all the lint, typecheck, and test targets need. Then `make lint` passed, `make typecheck` reported no issues in 76 source files, and `make test-unit` went from 375 passed / 53 xfailed without the fix to 376 passed / 52 xfailed with it, with no failures either way.

The posted plan comment did not mention the make targets, so it is still accurate and needs no follow-up comment.
