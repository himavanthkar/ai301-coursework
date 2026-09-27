# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56

**Verdict output**

```json
{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s1/issues/56",
  "checks": [
    {"name": "Recent commits", "grade": "pass", "evidence": "Last commit 2026-09-24, within 90 days"},
    {"name": "Maintainer responsive", "grade": "pass", "evidence": "Maintainer responses within days of issue opening"},
    {"name": "Bounded scope", "grade": "pass", "evidence": "Single class StructuralChunker.chunk(), one fallback branch"},
    {"name": "Not claimed", "grade": "pass", "evidence": "No assignee, no linked PRs, no claim comments"},
    {"name": "AI-friendly policy", "grade": "pass", "evidence": "No stated policy against AI tools in CONTRIBUTING.md"},
    {"name": "Good first issue label", "grade": "pass", "evidence": "Has 'good first issue' label"}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

First run: 14/20 (initial rubric too strict on "Bounded scope")
Second run (partial --only on 6 issues): 3/6 (policy check helped but scope still too strict)
Third run: 16/20 (loosened scope, policy now required)
Fourth run (full): 19/20 (final rubric, agreement: 19/20 scored items, bar: 18/20: PASS)

**Issue analysis**

issue-20: Gold label says reject (repo bans AI: "We do not accept AI-generated code or documentation"). My rubric graded: accept. 

My rubric accepted it because the "AI-friendly policy" check only looks for explicit bans in CONTRIBUTING.md, but issue-20's repo (BookWyrm) has a clear policy against AI. The check passed because I didn't read the policy carefully enough; the check should fail when policy explicitly forbids AI. This is the one remaining disagreement because the policy check is still too lenient — it treats "no stated policy" and "policy allows AI" as both pass, but should distinguish between them more carefully.

**Check rationale**

"Bounded scope | Issue is not explicitly labeled as umbrella/epic/tracking. Does not ask for implementation of a broad capability across many unrelated areas. Related tasks for one coherent feature are OK"

This check was initially too strict (rejecting legitimate multi-file doc updates like issue-01). Loosening it to allow "related tasks for one coherent feature" fixed the false rejections. The check now focuses on intent (is this one coherent piece of work?) rather than artifact count (how many files touched?).

**Trade-offs**

The loosened "Bounded scope" check gives up precision on umbrella issues. By allowing "related tasks for one coherent feature," the rubric now accepts some issues that involve multiple changes (docs updates, multiple related features) where a stricter definition would reject them. This trade-off is acceptable because the other required checks (maintainer responsive, not claimed, policy) catch most of the other failure modes. The remaining miss (issue-20) is in the policy check, not scope — tightening scope further would just add false rejections.

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

1. **Fit to interests and time:** Issue #56 is a good fit. It's a pure Python bug (no system dependencies), and the reproduction is a simple 3-line script. The test already exists and fails, so I know exactly what "done" looks like. The scope is narrow (one method, one edge case) so it's doable in a reasonable timeframe. I'm comfortable with Python and unit testing, so this aligns with my experience.

2. **What the verdict identified correctly:** The skill correctly identified that #56 has all the green flags: active maintainers (recent commits and responses), bounded scope (one class method), no one already working on it, and a clear reproduction. What the rubric couldn't see is the learning value — the skill grades on risk of getting stuck, not on how much you'll learn. This issue is ideal because it's low-risk AND teaches how to navigate a real codebase, write a minimal test case, and argue for a specific fix.

3. **Anticipated difficulty claiming it:** Low friction. The issue has been open since Sep 10 with zero engagement, so claiming it is straightforward (comment "I'll take this"). No competing PRs, no maintainer involvement yet. The only question is whether the repo maintainers want the fallback behavior I'll implement — but the issue body suggests the trade-off, so I'm not guessing.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.
