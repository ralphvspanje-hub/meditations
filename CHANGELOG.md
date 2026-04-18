# Marcus Changelog

## 2026-04-18 — brief-drop: Step 2b reviewer subagent pass

**What happened:** The prescriptive Step 1 and Step 2 rules partially fixed tag drift and under-extraction on large briefs, but a structural blindspot remained: the same Marcus instance that segments sections and picks theme tags cannot reliably catch its own segmentation or theme-selection errors. Single-pass self-review misses judgment-call cases even when the mechanical rules fire correctly.

**Changed:** Added Step 2b to `skills/brief-drop/SKILL.md`, running between Step 2's draft assembly and the proposal presentation. An independent reviewer subagent receives three inputs (raw dump, Marcus's draft proposal, four review criteria) and returns a strictly-formatted delta with sections for missed atoms, tag additions, entities to drop, and segmentation fixes. Marcus merges the delta mechanically. A delta-size gate (5+ atoms or 30%+ tag changes) surfaces large rewrites before applying. Conflicts with the Step 1/2 prescriptive rules resolve in favor of the prescriptive rules. Self-containment is preserved: if no subagent dispatch is available in the running environment, the step is skipped and the prescriptive rules alone carry the load. Self-observation triggers rewritten to focus on double-misses (both layers missed), subagent under-firing, and over-firing.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-18 — brief-drop: enumerated-list rule, section-grouping, sibling-tag-parallelism

**What happened:** Large external-import briefs surfaced two failure modes: sibling atoms from the same source-section drifted on tag vocabulary (so compile fragmented what should cluster), and items inside enumerated lists got collapsed under surface-similarity judgments (so distinct claims got dropped).

**Changed:** `skills/brief-drop/SKILL.md` Step 1 gains an enumerated-list rule (every item in a numbered or bulleted list becomes a candidate atom, suppressed only on strict word-level paraphrase) and a section-grouping pass that builds a source-section → candidate-atoms map as a visible artifact. Step 2 gains a sibling-tag-parallelism check that runs before the proposal is assembled: for each section with 2+ siblings, derive the section's primary theme tag and silently ensure every sibling carries it. Two residual self-observation triggers were added for cases the prescriptive checks might miss.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-16 — v1.0 initial public release

**What happened:** First public release of Meditations, an LLM-maintained personal learning wiki pattern. Based on Andrej Karpathy's "LLM Wiki" pattern and Farza's Farzapedia personalization principles.

**Changed:** Initial commit.

**File:** Everything.
