---
tags: [hidden]
---

# Brief captures

This directory holds verbatim captures of rich multi-claim dumps — narrative events (a hackathon, a long 1:1, a recruiter screen), catch-up end-of-day reflections where several things are dumped at once, or week-in-reviews too dense for a single observation.

Each file is a first-class wiki node. Briefs are not raw scratchpads; they are queryable, wikilinked, and show up in the Obsidian graph alongside concepts, entities, and observations.

## Relationship to observations

Briefs spawn atom observations. Every approved atom is filed to [`../observations/`](../observations/) with a `brief:` frontmatter backlink to its parent brief. The brief body carries the other direction under an `## Atoms` section as body wikilinks. Both directions live in the body because Obsidian's graph reads body wikilinks as edges, not frontmatter fields.

The blanket-brief-plus-specific-atom Mentions rule: every entity listed in a brief's `entities:` frontmatter gets one `## Mentions` entry pointing to the brief. Each entity in an atom's own `entities:` frontmatter (usually a subset of the brief's list) gets an additional `## Mentions` entry pointing to that specific atom. This keeps mention counts proportional to actual content.

Empty-atoms briefs are valid. A pure-narrative brief with `atoms: []` and `## Atoms` reading `_No atoms extracted from this brief._` is a legitimate use case — sometimes the narrative is the point.

## Frontmatter convention

See [AGENTS.md](../../AGENTS.md) section 6, "Brief frontmatter," for the authoritative schema.

## Internal by directory, provenance by field

Files in `wiki/briefs/` live alongside observations in the curated layer — they are always `sources_internal:` when cited from a concept or entity article, regardless of whether the claim originated from lived experience or was imported from an external source. Briefs default to `provenance: lived` when the `provenance:` field is not specified. When the field is set to `imported` or `unknown`, that value takes precedence over the location default. Imported briefs still live here — there is no folder split.

An atom inside a lived brief can itself be imported, because an atom's provenance reflects the claim's origin rather than the event's origin. When a concept article cites a brief-extracted atom as evidence, the atom slug goes under `sources_internal:` and the brief context plus the claim origin both live in the evidence-bullet prefix. A lived atom uses `[Experience, YYYY-MM-DD, from brief brief-slug]`; an imported atom uses `[Reading, source_ref, YYYY-MM-DD, from brief brief-slug]`. See AGENTS.md section 6, "Internal vs external sources," for the full provenance enum and the hybrid worked example.

Brief-drop is the only skill that writes here. See [`../../skills/brief-drop/SKILL.md`](../../skills/brief-drop/SKILL.md).
