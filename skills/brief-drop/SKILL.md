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

**Core criterion — each atom is a testable heuristic.** An atom is a claim that (a) could be evaluated true or false against a future situation or observation, and (b) can stand on its own without needing an adjacent claim to carry its meaning. Apply this criterion to every candidate span in the dump. Structural form in the source (paragraph breaks, enumerated lists, topic sentences) is a hint, not the decision. The functional test wins.

Three consequences flow from this criterion:

1. **If a candidate span fails (b)** — it depends on an adjacent claim to make sense or to have force — fold it into that adjacent atom as rationale, example, or qualifier. Do not file it as its own atom.
2. **If two candidate spans each pass (a) and (b) independently** — each can be evaluated on its own against future observations — they are distinct atoms, even when they share a paragraph or sit on opposite sides of a paragraph break.
3. **Enumerated items ((a)(b)(c), 1., 2., 3., bullets) nearly always pass the test.** The source chose to enumerate them precisely because each makes a distinct claim. Default to splitting enumerated items; suppress only when one item is a pure word-level paraphrase of another already-extracted atom.

**Four worked examples covering the shapes that recur in real briefs.**

**Example A — Enumerated sub-list inside a paragraph with a dominant thesis.**

Source: "Founders, founders, founders — Not one variable among many, but the variable. Three traits stood out beyond raw intelligence: (a) ability to adapt / rate of change — learning speed beats initial strategy; (b) ferocity / intensity — passion and drive; (c) founder-market fit — the sense that no one else could do this as well as them."

Atoms: **four**, not one.
- "Founders are THE variable, not one variable among many" — testable belief about the relative importance of founders vs other factors in a company's outcome.
- "(a) Adaptability / rate of change matters — learning speed beats initial strategy" — testable against any future founder's iteration record.
- "(b) Ferocity / intensity is a founder trait worth identifying" — testable against future founders' drive.
- "(c) Founder-market fit — no one else could do this as well as them" — testable against any founder-market pairing.

Why four: each (a)(b)(c) is an independently evaluable heuristic. The top-line claim is also a distinct heuristic — a belief about what matters most. The enumerated form reinforces the split but does not drive it; the functional test does. Collapsing (a)(b)(c) into the top-line atom would make the compiled concept article thinner and the future pattern-matching weaker.

**Example B — Trailing normative sentence after a paragraph's main claim.**

Source: "Criticizing in public, and build relationship in private. When you give feedback individually you are optimizing for the atomic unit not the system. ... A mix can be good like a sports coach. Psychological safety should not be a thing here."

Atoms: **two**, not one.
- "Criticize in public, build relationships in private — public feedback optimizes the system"
- "Psychological safety should not apply in this feedback culture"

Why two: the trailing sentence proposes a distinct normative heuristic (psych safety is not a requirement for winning feedback environments). It can be evaluated independently against future feedback environments. "Here" is grammatically referential but the heuristic stands on its own logic. If the user were asked "should psych safety apply?", they could answer without quoting the public-criticism claim. Two testable heuristics → two atoms.

**Example C — Rationale, caveat, or qualifier clause after a main claim.**

Source: "Push when the company is thriving, coach when struggling. Because people take better advantage of feedback when they are winning."

Atoms: **one**, not two.
- "Push when thriving, coach when struggling — people take better advantage of feedback when they are winning"

Why one: the "because" clause gives the reason for the main claim. Removed from the main claim, the reason loses its anchor; the main claim also loses its rationale. The clause is rationale, not a distinct heuristic. Fold it in. Same pattern for caveats ("unless X") and qualifiers ("in particular X", "specifically X" when it narrows the prior claim rather than introducing a new topic).

**Example D — Continuation across a paragraph break.**

Source: "Don't talk to consumer customers. Purchases are subconscious. [paragraph break] The best companies have a foundational insight. You can test that logic with users but you won't find it."

Atoms: **two, filed as siblings in the same section for tag purposes.**
- "Don't talk to consumer customers — purchase decisions are subconscious"
- "Foundational insight isn't discoverable via customer testing"

Why: two distinct heuristics (each evaluable on its own), but they argue within the same thesis — the limits of talking to customers/users. The paragraph break is prose rhythm, not a thesis boundary. File as two atoms, but group them in one section so tag parallelism propagates `customer-development` across both. The "specifically" adverb in other contexts, where "on references specifically asking..." opens a new topic (what to ask in references) rather than narrowing the prior claim, is the same shape: two atoms, same section.

**Section-grouping (for tag parallelism only, not for atom identification).** Group atoms that argue within the same thesis or about the same topic into one section, regardless of paragraph boundaries in the source. Sections drive Step 2's sibling-tag-parallelism check. They do not drive atom identification — the testable-heuristic criterion does that. A section can span paragraph breaks (Example D above). A section has 1 atom if no other atom shares its argument arc; it has 2+ atoms if other atoms do. Only sections with 2+ atoms matter for parallelism.

For each multi-atom section, note the primary theme tag(s) implied by the atoms' shared thesis or by the source heading that introduces them. These theme tags feed the Step 2 check.

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

**Sibling-tag-parallelism check (runs before the proposal is presented).** For each multi-atom section from Step 1's grouping, identify the primary theme tag(s) — what every atom in that section is a claim *about*. Verify every sibling carries at least one of those theme tags. If a sibling misses a theme tag its peers carry, add it before assembling the proposal. Silent fix — the user sees the consistent tags, not the drift-then-correction. The check does not rewrite each atom's local tags; it only ensures the shared theme tag is present. Local tags (specific to the atom's own claim) stay untouched.

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

### Step 2b — Reviewer subagent pass

Between Step 2's draft assembly and the proposal presentation to the user, dispatch an independent reviewer subagent. The subagent exists to break the single-pass self-review bias: the same Marcus instance that identifies sections and picks theme tags cannot reliably catch its own segmentation or theme-selection errors. Prescriptive rules from Step 1 and Step 2 catch the mechanical cases; the subagent catches the judgment-call cases where segmentation or theme selection is genuinely ambiguous. Both layers run; they fail on different axes.

**Dispatch.** Use the Agent tool (Claude Code) or the equivalent subagent-dispatch mechanism in non-Claude-Code environments. If no subagent dispatch is available, skip this step — the prescriptive rules carry the load and the user's approval step catches residual drift. Self-containment (BYOAI) is preserved: the subagent is an optimization, not a dependency.

**Prompt the subagent with exactly three things.**

1. The raw source dump, verbatim.
2. Marcus's draft proposal: title, brief provenance and source_ref, the section-grouping map from Step 1, and every atom with its title, verbatim quote, and tags.
3. The four review criteria below. No other instructions, no free-form "improve the proposal" framing.

**Four review criteria.** Apply the same core criterion Marcus applied in Step 1: each atom is a testable heuristic (claim evaluable against future observations, able to stand on its own). Check the draft from both directions and also for sibling-tag and entity drift.

- **Over-folded atoms.** Does any atom's quote-lift contain two or more distinct testable heuristics that could each stand on their own? The shape to catch: an enumerated sub-list ((a)(b)(c), 1/2/3, bullets) embedded inside an atom's quote; a trailing normative sentence tacked onto a paragraph's main claim; two different evaluable claims packed into one atom because they share a paragraph. Flag each as an ATOMS TO SPLIT entry with the candidate split points and proposed tags for each resulting atom. Reference Example A and Example B from Step 1 as the template.
- **Over-split atoms.** Does any atom's quote-lift fail to stand on its own as a testable heuristic — is it rationale, example, caveat, or qualifier for an adjacent atom? Read the candidate standalone; if removing it weakens an adjacent atom, or if it makes no sense without the adjacent atom's context, it's dependent. Flag each as an ATOMS TO MERGE entry with the parent atom it should fold into. Reference Example C from Step 1 as the template.
- **Sibling-tag parallelism.** For each multi-atom section, identify every primary theme tag — what every atom in the section is a claim *about*. Not the full union of peer tags, just the shared theme(s). Every sibling must carry at least one. Flag any missing theme tags as TAGS TO ADD.
- **Entity drift.** Is the draft proposing an entity stub for someone illustrative rather than interactive? Podcast speakers, authors cited for color, illustrative company examples are reference-origin at best and usually don't warrant a stub. Flag as ENTITIES TO DROP.

**Required subagent output format.** A structured delta, nothing else. Free-form prose, alternative segmentation narratives, or generic improvement suggestions are out of scope and must be ignored on return.

```
ATOMS TO SPLIT:
- Atom [title or index]: split into [N] atoms
  Split points: [brief description of where to split]
  Resulting atoms:
    1. Title: [...]
       Quote: "[verbatim span from original atom]"
       Tags: [...]
    2. Title: [...]
       Quote: "[verbatim span from original atom]"
       Tags: [...]
  Reason: each resulting atom is a distinct testable heuristic

ATOMS TO MERGE:
- Atom [title or index]: merge back into atom [adjacent title or index]
  Reason: quote is [rationale / example / caveat / qualifier] dependent on the parent atom — fails the standalone-heuristic test

TAGS TO ADD:
- Atom [title or index]: add [tag1, tag2]
  Reason: shared theme of section "[section label]" which peer atoms carry

ENTITIES TO DROP:
- [entity-slug] — reason: [illustrative reference / podcast speaker / etc.]

If a criterion has no findings, output that criterion heading followed by "none".
```

**Merge behavior.** Marcus applies the delta mechanically:

- Atoms to split: replace the original atom with the N resulting atoms. Each resulting atom takes its proposed title, its verbatim quote span from the original, and its proposed tags. Re-run the Step 2 sibling-tag-parallelism check on the expanded list so shared theme tags propagate correctly.
- Atoms to merge: remove the dependent atom from the list and append its quote text to the parent atom's quote-lift, preserving verbatim phrasing and inserting a space or paragraph break as the source dump does. No tag changes on the surviving parent unless the delta also includes a TAGS TO ADD line for it.
- Tags to add: append the named tags to the named atoms. No removal or reordering.
- Entities to drop: remove them from the brief's `entities:` list and skip any stub creation for them.

**Delta size gate.** If the merged delta adds 5+ atoms or changes tags on more than 30% of atoms, Marcus does not merge silently. Instead, present the delta to the user before applying, prefixed: "Reviewer flagged [N] changes; large enough to surface before merging. Approve, edit, or reject." This avoids silent wholesale rewrites.

**Conflict resolution.** If the subagent's delta contradicts the prescriptive rules from Step 1 or Step 2 (e.g. proposes collapsing two enumerated-list items into one atom), the prescriptive rule wins and the conflicting delta item is dropped. Log the conflict in CHANGELOG.md so the rule or the subagent prompt can be refined.

**Single-turn budget.** Dispatch once per brief. No iterative refinement with the subagent — if its delta is obviously broken, Marcus drops the step and proceeds on the Step 1/2 draft alone. Iteration bloat is a failure mode worse than occasional misses.

**Present final proposal to the user. This step is mandatory and never skipped.** After the subagent delta has been merged (silently under the delta-size gate, or after the user's approve/edit/reject on the size-gate-tripped path), Marcus ALWAYS presents the final proposal to the user in the Step 2 format — full title, provenance, entities, and every atom with its title, verbatim quote, and tags. The silent-merge path is silent with respect to the subagent's delta, not with respect to the proposal itself. The delta-size gate's user-prompt is an extra checkpoint for large subagent changes; it does not replace the final proposal-approval checkpoint.

End the presentation with: "Approve, edit, or remove atoms. Approve or edit the title. Proceeding to filing only on explicit approval." Then wait. Do not proceed to Step 3 until the user responds with approval (with or without edits). If the user edits the proposal (removes an atom, changes a tag, adds an entity), apply the edits and re-present once for final sign-off. No files hit disk before explicit approval.

### Step 3 — Write the brief file

**Precondition — verify approval before writing.** Step 3 never runs without explicit approval from the user on the final post-subagent proposal. If Marcus arrives at Step 3 without a clear approval message in the conversation ("yes", "approve", "proceed", "file it", or substantive edits followed by approval), stop and return to the end of Step 2b to present the proposal. A silent subagent merge is not approval. A size-gate approval on the subagent's delta alone is not approval of the final proposal. Only the user's explicit go-ahead on the final proposal counts.

Load-bearing reason: silent subagent merges under the size gate can look like a proceed signal when they are not. Without this precondition, Marcus can file files to disk before the user has seen the final shape of the atoms. This rule closes that hole.

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
- If the user finds an over-folded atom at approval time (a single atom whose quote contains two or more distinct testable heuristics that should each stand on their own) that both Step 1 and the Step 2b subagent missed, log it. The worked examples in Step 1 (especially Example A for enumerated sub-lists and Example B for trailing normative sentences) may need another shape added.
- If the user finds an over-split atom (quote is rationale, example, caveat, or qualifier for an adjacent atom) that both Step 1 and the subagent missed, log it. Example C may need another shape added.
- If the user finds a sibling-tag gap that both Step 2 and the subagent missed, log it. Example D's continuation-across-paragraphs treatment may need another shape added.
- If the user consistently splits or merges atoms the opposite way from the proposal three times across different briefs, the testable-heuristic criterion or the worked examples need re-tuning against the user's actual preferences.
- If the Step 2b subagent returns an empty delta but the user then flags real gaps at approval time, the subagent's prompt or the criteria wording is under-calibrated.
- If the Step 2b subagent returns a delta so large it trips the 5-atoms-or-30%-tags gate on 3+ briefs, the subagent is over-firing. Review criteria may be too permissive.
