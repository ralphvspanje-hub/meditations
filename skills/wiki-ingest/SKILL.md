---
name: marcus-wiki-ingest
description: "Ingest a raw source (book, article, PDF, web clip) into the Meditations wiki as a summary page plus updates across affected entity and concept pages"
tags: [ingest, wiki, sources, reading, external]
user-invocable: true
metadata:
  openclaw:
    emoji: "📥"
---

# Wiki Ingest Skill

## When to trigger

**Explicit trigger:** the user says `ingest:` followed by a path to a file in `raw/`.

Examples:
- "ingest: raw/inspired-2017-summary-cagan.md"
- "ingest raw/some-article.md"
- "ingest: raw/paper.pdf"

`ingest:` ALWAYS routes to this skill. It never routes to observation-drop. It never routes to the Claude Code auto-memory system. See [CLAUDE.md](../../CLAUDE.md) hard rules.

**Proactive trigger:** when the user drops a new file into `raw/` and mentions it in conversation (even without the `ingest:` prefix), offer once:

> "That looks like a new raw source. Want me to ingest it into the wiki?"

If yes, run this skill. If no or the user changes the subject, drop it and do not re-ask.

**Do NOT trigger on:**
- Files outside `raw/` — raw sources must live in the raw layer first.
- Casual mentions of books or articles the user has not actually placed in `raw/`.
- Questions about a source (those route to wiki-query).
- Saving an observation that references a source (that is observation-drop; observations are internal).

Never auto-trigger without the user explicitly saying so.

## What to read

| File | Why |
|---|---|
| The target file in `raw/` | The source itself. For PDFs over 10 pages, use the Read tool's `pages` parameter to chunk. |
| `wiki/INDEX.md` | Check existing concepts, entities, and sources |
| `wiki/log.md` | Recent activity, last ingests, context |
| `wiki/entities/*` | Any entity page the source might update or create |
| `wiki/concepts/*` | Any concept page the source might inform |
| `wiki/observations/` | Any existing observation referencing the same source — cross-link it |
| [AGENTS.md](../../AGENTS.md) section 6 | The internal/external source convention |

## What to write

| File | When |
|---|---|
| `wiki/sources/[slug].md` | Every ingest — one summary page per raw source |
| `wiki/entities/*/*.md` | Create or update entities the source introduces or informs |
| `wiki/concepts/*.md` | Create or update concept articles the source strengthens (bounded — prefer keeping content in the summary page unless a concept is already established) |
| `wiki/log.md` | Append an `ingest` entry |
| `wiki/INDEX.md` | Add the summary page under `## Sources` |

Never modify any file in `raw/`. Raw sources are immutable ground truth.

## Behavior

### Step 1 — Read the raw source

Read the target file completely. For PDFs over 10 pages, use the `pages` parameter to chunk the read. Note structure (chapters, sections, key arguments).

### Step 2 — Discuss key takeaways with the user

**Do not silently summarize.** Present 3-7 takeaways from the source in chat and ask which ones matter most for the user's current context (projects, interests, specific goals). Ask which framings land and which do not. This is where the ingest becomes the user's, not the author's.

**Opt-out path:** if the user says "I have already read it, skip discussion" or similar, proceed directly to Step 3. Offer this path explicitly — but never default to skipping. The discussion step is where the user's pushback and framing are captured.

**If the user pushes back on a claim during discussion:** disagreement is primary evidence about how the user thinks. Offer to save the pushback as a new observation in `wiki/observations/`. Do not silently drop it.

### Step 3 — Write the summary page

Create `wiki/sources/[slug].md`. Slug is a short lowercase-hyphenated identifier: `inspired-2017-cagan`, `good-strategy-bad-strategy`, `hbr-on-jobs-to-be-done`.

Frontmatter:

```yaml
---
title: [Source title]
type: source-summary
created: YYYY-MM-DD
updated: YYYY-MM-DD
author: [author or organization]
citation: [path to the raw file, e.g. raw/inspired-2017-summary-cagan.md]
sources_internal: [list of observation slugs that reference this source, if any]
tags: [topic tags]
---
```

Body structure:

```markdown
# [Source title]

**What it is:** [one-line description — type of source, author, year, why it matters to the user]

**Citation:** [`raw/...`](../../raw/...) — the immutable source file.

## Key ideas

[3-8 key ideas in user-relevant framing. Not a chapter-by-chapter recap.
Think: what does the user need to have internalised from this source.]

## Notable quotes

[A handful of direct quotes worth preserving verbatim. Cite chapter or page if known.]

## Applied to the user's context

[How the ideas connect to the user's interests, projects, goals, or existing concepts. This is where the source becomes useful, not just read.]

## Open questions

[What the source leaves unanswered for the user — things to test against later
observations, or questions to bring to a conversation.]

## Related

- [[entities/people/author-slug]] — if created
- [[concept-slug]] — any concept this source informs
- [[observations/YYYY-MM-DD-slug]] — any pre-existing observation about this reading
```

**Cap the body under ~300 lines.** Link back to the raw file for detail — do not duplicate the source. The summary is the user's view of the source, not a transcription.

### Step 4 — Update or create affected entity and concept pages

Identify affected pages from the discussion. For each:

**New entity page (e.g. the author):**
- Create under `wiki/entities/people/`, `companies/`, or `projects/`.
- Set `origin: reference` in frontmatter.
- Add to `sources_external:` a reference to this source (e.g. `Cagan 2017 — Inspired`).
- Leave `sources_internal:` empty unless a pre-existing observation already mentions the entity.

**Existing entity page:**
- Append a line under `## Mentions` referencing the source.
- Add the source reference to `sources_external:` only. Never change `origin:`. Never overwrite `sources_internal:`.
- **Collision rule:** if the entity exists with `origin: experienced`, do not change `origin:`. Add the source reference to `sources_external:` alongside whatever is already in `sources_internal:`. Flag to the user so they are aware the entity now has grounding from both experience and reading — but no frontmatter enum value changes. The `sources_internal:` / `sources_external:` split already carries the mixed-grounding signal. No fourth origin value is needed.

**Existing concept page:**
- Add an evidence bullet under `## Evidence` prefixed `[Reading, short-source-ref]` (e.g. `[Reading, Cagan 2017 ch 33]`).
- Add the source reference to `sources_external:` on the concept page.
- Rewrite affected sections if the source genuinely changes the synthesis, following the rewrite rules in AGENTS.md section 3 (preserve `<!-- user-framing: true -->` sections verbatim).

**New concept article (rare — be conservative):**
- Only create if the source establishes a pattern that (a) the user confirms is useful and (b) is likely to accumulate more evidence later. A concept article with one external source and zero observations is thin. Prefer keeping the material inside the summary page until observations accumulate.
- Propose before creating. Do not auto-file.

### Step 5 — Cross-link pre-existing observations

If an observation in `wiki/observations/` already references the same source:
- Add its slug to the summary page's `sources_internal:` frontmatter.
- Mention the observation in the summary body (under `## Related` or in the applied-to-the-user section).
- Leave the observation file itself untouched.

### Step 6 — Append to wiki/log.md

```
## [YYYY-MM-DD] ingest | [source-slug] — [N pages touched, brief note]
```

### Step 7 — Update wiki/INDEX.md

Add the summary page to the `## Sources` section with a one-line description. Keep entries alphabetical or reverse-chronological — whichever matches the existing style.

### Step 8 — Close out

Short summary listing every page touched:
- Summary page created: [slug]
- Entities created: [list]
- Entities updated: [list]
- Concepts created or updated: [list]
- Observations cross-linked: [list]
- Log entry appended, INDEX updated.

Ask: "Anything in this summary you want to push back on or reshape?" to keep the door open for further user framing.

## Internal vs external source convention

Wiki-ingest populates `sources_external:` on any concept or entity page it touches. It never populates `sources_internal:` except when cross-linking a pre-existing observation to a source summary page.

When creating new entity pages, wiki-ingest sets `origin: reference`.

Evidence bullets added to concept articles from an ingest are prefixed `[Reading, short-source-ref]` — e.g. `[Reading, Cagan 2017 ch 33]`.

wiki-compile does the mirror: it populates `sources_internal:`, sets `origin: experienced` on entities that emerge from direct experience, and prefixes evidence bullets with `[Experience, YYYY-MM-DD]`.

Both skills read this convention from AGENTS.md section 6. If in doubt, re-read that section.

## Edge cases

- **Very large PDFs.** Use the Read tool's `pages` parameter to chunk. Do not try to read a 400-page book in one pass.
- **Partial ingest.** If the user only wants certain sections covered, document which sections were ingested in the summary page body. Leave the rest for later.
- **Duplicate source.** If the raw file covers the same material as an existing summary page, flag it: "We already have a summary for [X]. Merge, replace, or ingest as a separate source?" Do not silently merge.
- **User has already read it.** Offer the opt-out path for Step 2 explicitly. Do not default to skipping.
- **Collision with experienced entity.** If the source would touch an entity already marked `origin: experienced`, do not change `origin:`. Add the source to `sources_external:` alongside existing `sources_internal:`. Flag for awareness. The provenance split in frontmatter carries the mixed-grounding signal without needing a new enum value.
- **User pushes back on a claim.** Offer to save the pushback as an observation. Disagreement is data.
- **Pre-existing observation on the same source.** Cross-link it into `sources_internal:` on the summary page and mention it in the body.
- **Source introduces concepts the user does not want formalised.** Keep them inside the summary page. Do not force concept article creation.
- **Raw file is not in `raw/`.** Ask the user to move it there first. Ingest reads from the raw layer, not from arbitrary locations.

## Self-observation triggers

Append to CHANGELOG.md if:
- An ingest produced an unusually large number of touched pages (signal: the source is too big for one ingest, or the convention is over-fragmenting).
- The user frequently rejects proposed concept creations from ingested sources (signal: the concept-promotion threshold is set too low).
- The discussion step (Step 2) is repeatedly being skipped (signal: the opt-out is becoming the default — re-check the framing).
- An ingest surfaces a contradiction with an existing concept article (log it like wiki-compile logs contradictions).
