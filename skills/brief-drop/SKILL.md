---
name: marcus-brief-drop
description: "Capture a narrative or catch-up dump verbatim and extract atom observations from it"
tags: [capture, brief, observation, wiki]
user-invocable: true
metadata:
  openclaw:
    emoji: "📓"
---

# Brief Drop Skill

## When to trigger

**Explicit trigger:** the user uses `brief:` (or "Brief:") followed by content.

Examples:
- "brief: today I had a workshop and [long dump]"
- "brief: [end-of-day catch-up with multiple things I noticed today]"
- "brief [week in review]"

`brief:` ALWAYS routes to this skill. Never routes to observation-drop. Never triggers auto-memory. See [CLAUDE.md](../../CLAUDE.md) hard rule #4.

**Handoff trigger:** observation-drop hands off to this skill when the user accepts the brief-mode offer on a long multi-claim `save:`.

**No independent proactive trigger.** The proactive entry point for all captures lives in observation-drop. This skill only runs on explicit `brief:` or on handoff from observation-drop.

**Do NOT trigger on:**
- Casual uses of the word "brief" (like "give me a brief summary")
- Short single-claim content (that is `save:`)
- External sources (that is `ingest:`)

## What to read

| File | Why |
|---|---|
| `wiki/INDEX.md` | Check existing entities, concepts, briefs |
| `wiki/entities/` | Look up existing entity pages named in the dump |
| `wiki/briefs/` | Recent briefs, duplicate check |
| `wiki/log.md` | Recent activity |
| [AGENTS.md](../../AGENTS.md) section 6 | Brief frontmatter schema and conventions |

## What to write

| File | When |
|---|---|
| `wiki/briefs/YYYY-MM-DD-[slug].md` | Every brief, one file per capture |
| `wiki/observations/YYYY-MM-DD-[atom-slug].md` | One file per approved atom |
| `wiki/entities/[category]/[slug].md` | Update Mentions per the blanket-brief-plus-specific-atom rule |
| `wiki/log.md` | Append a `brief` entry |
| `wiki/INDEX.md` | Add the brief under `## Briefs` |

## Behavior

### Step 1 — Parse the dump

Read the content verbatim. Identify:
- The date (default today)
- Named entities (people, companies, projects)
- Rough topic tags
- A candidate one-line title for the brief
- Candidate atoms: sentences or short passages in the dump that each make one distinct claim

Preserve the user's phrasing exactly. Do not rewrite, summarize, or clean up.

**Enumerated-list rule.** When the source dump contains a numbered or bulleted list, every item in that list becomes a candidate atom. Do not collapse an item into an earlier atom on grounds of adjacency, thematic overlap, or surface similarity. An item from an enumerated list is suppressed only when it is a pure word-level paraphrase of an atom already extracted from a different source section — the threshold is strict. Items that restate a claim with new framing, new examples, or a different mechanism are distinct claims and get their own atom. The user or the speaker chose to enumerate the items separately; that choice is a signal that the items are claim-level-distinct, even when they share vocabulary with earlier material.

**Section-grouping pass.** Build a map of source-dump section → candidate atoms. Sections are identified by any of the following structural signals:

- A numbered or bulleted list header (e.g. "3 patterns of people that join unicorns:", "Traits of successful startups:").
- An explicit topic sentence or heading ("A vision on the future of pm:", "His advice is, unless you are building an enterprise company...", "What is a sign for a good AI idea/startup:").
- A run of 2+ sentences on one topic before a clear topic shift in the dump prose.

For each identified section, record (section label, primary theme tag(s) implied by the section's heading or opening sentence, list of candidate atoms originating from that section). Hold the map alongside the candidate atoms list as a visible artifact — it drives the sibling-tag-parallelism check in Step 2. Sections with only one candidate atom can be skipped (no siblings, no parallelism to check). Sections with 2+ siblings are the targets.

**Brief-level provenance detection.** Assess the dump as a whole. Default is `provenance: lived` — most briefs are the user narrating their own day, week, or event in their own voice. Flag the whole brief as a non-default-provenance candidate only when the narrative overall is an external import (for example, a long quote from a podcast episode pasted as a brief with `brief:`). In practice this is rare.

**Per-atom cue detection.** For each candidate atom, scan the quote-lifted span for all three cues from observation-drop Step 1:

(a) Third-person heuristic register ("a PM who...", "good founders...", "the best designers...").
(b) Explicit attribution phrasing ("X said", a named speaker, a named publication).
(c) First-person absent across the atom span (no "I", "me", "my", "we" inside the quote-lifted text itself — the surrounding brief body does not count).

When all three hold on a specific atom candidate, mark it as a mixed-provenance candidate. Marks carry into Step 2 as per-atom override prompts. Same narrow, high-false-negative discipline as observation-drop — the prompt exists for the loud cases only.

**Typo-fix pass.** Before writing, run a light spelling correction over the dump body (not the frontmatter).
- Allowed: fix obvious single-word typos where one clearly intended word exists (e.g. `ervything` → `everything`, `teh` → `the`, `recieve` → `receive`, `alot` → `a lot`).
- Forbidden: changing word choice, phrasing, grammar constructions, verb tense, register, or any stylistic choice. If unsure whether a word is a typo or an intentional choice, leave it.
- Never touch: proper nouns, brand names, technical terms, non-English words, domain names, code identifiers. When in doubt, skip.
- One-shot, no per-fix approval prompts. Silent unless fixes were made.
- After writing, the Step 8 acknowledgement must include a diff line when fixes were applied at the brief level or across atoms: `Fixed N typos in brief, M across atoms.` If zero, omit.

### Step 2 — Propose title and atoms in one shot

**Sibling-tag-parallelism check (runs before the proposal is presented).** For each source-section with 2+ candidate atoms from the section-grouping pass in Step 1, identify the section's primary theme tag(s) — usually derivable from the section's heading or opening sentence (e.g. "Traits of successful startups:" implies `startups`; "3 patterns of people that join unicorns:" implies `investing` alongside `founders`; "unless you are building an enterprise company, don't talk to your customers" implies `customer-development`; "A vision on the future of pm:" implies `future-of-work` alongside `pm`). Verify that every sibling atom in the section carries at least one of the section's theme tags. If a sibling is missing a theme tag its peers carry, add it before assembling the proposal. The fix happens silently during pre-proposal assembly — the user sees the already-consistent tags in the proposal, not the drift-then-correction. Only flag to the user if the check surfaces ambiguity about which tag is the section's actual theme (rare; usually the heading answers it).

This check does not rewrite each atom's local tags — it only ensures the parent-section tag is present. Local tags (specific to the atom's own claim) stay untouched. Compile's clustering benefits from the parallel theme tag without losing per-atom specificity.

Present in chat as a single message:

1. A proposed one-line brief title.
2. A numbered list of candidate atoms. Each atom has a short title, a verbatim quote-lift from the dump (sentence or paragraph), and rough tags.

Example format:

> **Proposed brief title:** [one line]
>
> **Brief provenance:** lived  _(default — override only if the whole dump is an external import)_
>
> **Proposed atoms:**
>
> 1. **[atom title]**
>    Quote: "[verbatim from the dump]"
>    Tags: [tag, tag]
>
> 2. **[atom title]**
>    Quote: "[verbatim from the dump]"
>    Tags: [tag, tag]
>
> Approve, edit, or remove each. Approve or edit the title.

**Per-atom override prompts.** If Step 1 flagged any atom candidate as mixed-provenance, add one line per flagged atom inside the proposal:

> Atom [N] looks imported — override to `imported` with source_ref? (one word is fine, or "no")

Only fire these prompts on flagged atoms. Atoms without all three cues get no prompt — their provenance inherits from the brief silently.

This is a one-shot approval. Do not conduct a long back-and-forth. Wiki-ingest does deep discussion on external sources; brief-drop does not. Briefs are often catch-up dumps and the capture should stay low-ceremony.

**Opt-out:** if the user says "skip, just file the narrative" or similar, file the brief with zero atoms. Empty-atoms briefs are valid.

**Verbatim rule:** every atom quote is a direct lift from the dump. If a proposed atom is a paraphrase, re-propose with a real quote-lift instead. Atoms preserve voice.

### Step 3 — Write the brief file

Create `wiki/briefs/YYYY-MM-DD-[slug].md`:

```markdown
---
title: [approved title]
type: brief
date: YYYY-MM-DD
updated: YYYY-MM-DD
entities: [list of entity slugs, include me if applicable]
tags: [topic tags]
atoms: [list of approved atom slugs, or empty list]
provenance: lived
source_ref: ""
---

# [approved title]

[The verbatim dump as the user wrote it, preserved exactly. Do not edit.]

## Atoms

- [[observations/YYYY-MM-DD-atom-slug]] — [one-line framing matching the atom title]
- [[observations/YYYY-MM-DD-atom-slug]] — [one-line framing matching the atom title]
```

If `atoms:` is empty, the `## Atoms` section contains `_No atoms extracted from this brief._`

Slug is a 3-5 word lowercase-hyphenated summary of the brief title.

### Step 4 — Write each approved atom

The typo-fix pass runs independently on each atom quote-lift, using the same rules as Step 1. Atoms preserve voice but mechanical typos get cleaned.

For each approved atom, create `wiki/observations/YYYY-MM-DD-[atom-slug].md`:

```markdown
---
date: YYYY-MM-DD
entities: [atom-specific entities, usually a subset of brief's entities]
tags: [atom-specific tags]
processed: false
brief: YYYY-MM-DD-[brief-slug]
provenance: lived
source_ref: ""
---

# [atom title]

_From [[briefs/YYYY-MM-DD-brief-slug]]_

[The verbatim quote-lift from the brief. If the quote needs one sentence of framing to stand alone, add it before the quote. Do not paraphrase the quote itself.]
```

The `_From [[briefs/...]]_` body wikilink is load-bearing. Obsidian's graph reads body wikilinks as edges, not frontmatter fields. Without this line the atom→brief edge does not appear in the graph view.

**Atom provenance rule (applies on every atom write, not only when Step 2's override prompt fires).** Atom provenance reflects the claim's origin, not the event's origin. A lived brief can contain an imported atom when the atom quote-lifts a claim attributed to someone or something external. By default, atoms inherit the brief's `provenance:` and `source_ref:`. Override per-atom only when the user answered an override prompt in Step 2 — in that case, set `provenance: imported` and `source_ref:` to their answer, keeping the atom file in the same directory as every other observation.

Filename collision: if a slug collides with an existing file on the same date, append `-2`, `-3`, etc. Same convention as `save:`-captured observations.

### Step 5 — Update entity Mentions (blanket-brief-plus-specific-atom rule)

For each entity in the brief's `entities:` frontmatter, append to that entity's `## Mentions` section:

```
- [[briefs/YYYY-MM-DD-brief-slug]] — YYYY-MM-DD brief — [one-line framing]
```

For each entity in an atom's `entities:` frontmatter (NOT the brief's blanket list — only the entities the specific atom names), append to that entity's `## Mentions` section:

```
- [[observations/YYYY-MM-DD-atom-slug]] — YYYY-MM-DD — [one-line framing]
```

Rule: brief gets a blanket mention on every entity in the brief frontmatter. Atoms get mentions only on entities they specifically name. Keeps the mention count proportional to content.

If a referenced entity has no page yet, create a stub per observation-drop conventions.

### Step 6 — Append to `wiki/log.md`

```
## [YYYY-MM-DD] brief | [brief-slug] — [N atoms extracted, M entities touched]
```

### Step 7 — Update `wiki/INDEX.md`

Add the brief under `## Briefs`:

```
- [[briefs/YYYY-MM-DD-brief-slug]] — [one-line title] (YYYY-MM-DD)
```

Update the last-updated stamp at the top of INDEX.md.

### Step 8 — Acknowledge

Short one-line acknowledgement, calm tone:

> "Brief filed with [N] atoms. [M] entity pages updated. [total unprocessed observations] observations unprocessed."

If the typo-fix pass applied any fixes, append a diff line: `Fixed N typos in brief, M across atoms.` If zero, omit.

If unprocessed observations cross 5+, mention it once:

> "You have [N] unprocessed observations. Want me to compile them into patterns?"

Do not auto-run compile. Do not recap the brief back to the user.

## Edge cases

- **`brief:` with no content:** ask "What do you want to file?"
- **Single-claim content via `brief:`:** still file as a brief with one atom. Do not reroute to save. The user chose the trigger.
- **Empty-atoms brief:** valid. File the narrative, skip Step 4, entity Mentions still fire for the brief per the blanket rule.
- **Atom quote spans a whole paragraph:** fine. Quote-lifts can be any self-contained unit from a sentence up to a paragraph.
- **Two atoms quote overlapping text:** fine. Atoms are claim-level, not text-exclusive.
- **Brief contains an unknown entity:** create a stub page per observation-drop conventions, link it from the brief and from each atom that names it.
- **User rejects or edits a proposed atom:** accept immediately, do not defend.
- **A proposed atom is a paraphrase not a quote-lift:** reject internally and re-propose with a real quote-lift.
- **Duplicate brief on the same day:** flag to the user: "You filed a brief today already ([slug]). Merge, or file as a separate brief?"
- **Observation-drop hands off mid-save:** receive the parsed content from observation-drop, proceed from Step 2.

## Self-observation triggers

Append to CHANGELOG.md if:
- The user consistently removes the same type of atom (Marcus is over-extracting)
- The user consistently adds atoms Marcus missed (Marcus is under-extracting)
- The one-shot discussion step turns into repeated back-and-forth rounds (one-shot style may be wrong for briefs)
- A brief repeatedly produces atoms that all cluster together during compile (single-event grounding fires often; track it but expected)
- Empty-atoms briefs become the dominant mode (brief-drop may be doing the wrong job for how the user actually uses it)
- Single-entity brief Mentions noise: if a brief and all its atoms name the same single entity, the Mentions section on that entity becomes a flat list of (1 brief + N atoms) all pointing to the same place. Watch whether this feels noisy after 3+ single-entity briefs. If yes, consider nested-list formatting (brief as parent bullet, atoms as indented children) rather than dropping atom mentions. Do not change behavior on one data point.
- If override prompts from Step 2 fire but the user consistently declines them, the per-atom cue detection is over-firing. Narrow the cues or raise the threshold. One decline is not enough; watch for a pattern across 3+ briefs before touching the cue list.
- If the user pushes back on a typo fix (says "that was intentional" or "put it back"), log it in CHANGELOG.md. Three pushbacks across different briefs means the rules are too loose — tighten.
- If the user finds a sibling-tag gap that the Step 2 sibling-tag-parallelism check did not catch, log the edge case in CHANGELOG.md. The check relies on section heading or opening sentence to derive the theme tag; if the section is inferred from prose cadence rather than an explicit heading, the theme tag may be ambiguous. Three misses across different briefs means the section-identification heuristics in Step 1 are too narrow and should be widened.
- If the user finds a missing atom from an enumerated list that Step 1's enumerated-list rule should have caught, log the edge case in CHANGELOG.md. The rule assumes every enumerated item is a distinct claim unless pure word-level paraphrase; if a list contains a true duplicate that should have collapsed and the rule forced an atom anyway, the "pure paraphrase" threshold may be too tight. Three misses across different briefs means the paraphrase threshold needs reexamination.
