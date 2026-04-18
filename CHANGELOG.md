# Marcus Changelog

## 2026-04-18 — brief-drop: enumerated-list rule, section-grouping, sibling-tag-parallelism

**What happened:** Large external-import briefs surfaced two failure modes: sibling atoms from the same source-section drifted on tag vocabulary (so compile fragmented what should cluster), and items inside enumerated lists got collapsed under surface-similarity judgments (so distinct claims got dropped).

**Changed:** `skills/brief-drop/SKILL.md` Step 1 gains an enumerated-list rule (every item in a numbered or bulleted list becomes a candidate atom, suppressed only on strict word-level paraphrase) and a section-grouping pass that builds a source-section → candidate-atoms map as a visible artifact. Step 2 gains a sibling-tag-parallelism check that runs before the proposal is assembled: for each section with 2+ siblings, derive the section's primary theme tag and silently ensure every sibling carries it. Two residual self-observation triggers were added for cases the prescriptive checks might miss.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-16 — v1.0 initial public release

**What happened:** First public release of Meditations, an LLM-maintained personal learning wiki pattern. Based on Andrej Karpathy's "LLM Wiki" pattern and Farza's Farzapedia personalization principles.

**Changed:** Initial commit.

**File:** Everything.
