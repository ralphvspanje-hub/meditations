---
name: marcus-wiki-query
description: "Answer questions using the Meditations wiki as knowledge base"
tags: [wiki, query, search]
user-invocable: true
metadata:
  openclaw:
    emoji: "❓"
---

# Wiki Query Skill

## When to trigger

User starts a message with or asks:
- "wiki:"
- "look up:"
- "what do I know about..."
- "how have I handled..."
- "tell me about [person / company / topic]"
- "what have I learned about..."
- "search: [topic]"

Examples:
- "wiki: giving feedback"
- "what do I know about Acme Corp?"
- "how have I handled disagreement with managers before?"
- "look up: a coworker name"
- "tell me about running retros"

Do NOT trigger on general conversation that happens to mention a wiki topic. Only trigger on explicit question intent.

## What to read

| File | Why |
|---|---|
| `wiki/INDEX.md` | Find relevant articles by category |
| Matched `wiki/concepts/*.md` | Full article content |
| Matched `wiki/entities/*/*.md` | Entity-specific context |
| Matched `wiki/briefs/*.md` | Narrative-level captures for "tell me about the [event/day]" queries |
| Recent `wiki/observations/*.md` (last 14 days) | Uncompiled material that might be relevant |
| `wiki/log.md` | Recent activity for context |

## What to write

| File | When |
|---|---|
| `wiki/queries/YYYY-MM-DD-[slug].md` | When the answer is substantial AND the user agrees to file it |
| `wiki/log.md` | Append a `query` entry every time, regardless of filing |

## Behavior

### Step 1 — Parse the query

Identify the query type:
- **Entity-based:** "tell me about Acme Corp", "what do I know about a teammate"
- **Concept-based:** "how do I give feedback", "what works for running retros"
- **Cross-cutting:** "when has X come up before", "have I seen this pattern before"

### Step 2 — Search INDEX.md

Match against article titles, tags, and summaries across concepts, entities, sources, briefs, and queries. Identify 1 to 5 candidate articles. Briefs are narrative-level captures; match against them for event or day-level queries.

### Step 3 — Read matched articles in full

Do not just skim summaries. Read the actual articles, including `## Edges and exceptions` and `## Contradictions` sections if present. These are often the most valuable part of the answer.

### Step 4 — Check uncompiled observations

Scan recent observations (last 14 days, `processed: false`) for mentions of the query topic. Uncompiled material is raw but may contain the most recent signal. Flag it as "not yet compiled" in the answer.

### Step 5 — Synthesize the answer

Write an answer that:
- Cites with wikilinks: `"Based on [[giving-direct-feedback]] and your observation from 2026-04-07..."`
- Calls out contradictions if relevant: `"There is a flagged contradiction here — [[a-teammate]] does not respond to the pattern that worked with others."`
- Mentions uncompiled observations if they matter: "There is a recent observation from [date] that is not yet compiled: [brief]."
- Stays calm and direct. No hype. No unnecessary preamble.
- **Narrative queries vs pattern queries.** Narrative queries ("tell me about the workshop," "what happened on the recruiter screen day") land primarily on briefs. Pattern queries ("when have I been good at X," "how do I handle Y") land primarily on concepts and atoms. Follow brief→atom wikilinks when a narrative query needs to surface specific claims, and follow atom→brief wikilinks when a pattern query needs event context.
- **Imported-provenance claims carry their source_ref.** When the answer cites an observation or atom whose `provenance:` is `imported`, include the atom's `source_ref:` value in the prose (e.g. "per a panelist" or "per Cagan"). A `lived` claim stands on the user's own authority; an `imported` claim must carry its provenance pointer so the reader can tell the difference.

### Step 6 — Offer to file

If the answer is substantial (a synthesis across 3+ articles, a comparison, a new framing the user did not explicitly save), ask:
> "This turned into a useful synthesis. Want me to file it to `queries/` so it compounds?"

For simple lookups (single article, direct answer), do not offer to file. That would be noise.

### Step 7 — Log the query

Append to `wiki/log.md`:
```
## [YYYY-MM-DD] query | [one-line question summary]
```

Log regardless of whether the answer was filed.

## Filing a query

When the user agrees to file, write `wiki/queries/YYYY-MM-DD-[slug].md`:

```markdown
---
date: YYYY-MM-DD
question: [the question the user asked]
sources_internal: [list of observation slugs referenced]
sources_external: [list of source summary pages or external references, empty if none]
tags: [relevant tags]
---

# [Short title capturing the question]

**Question:** [the user's question, or a cleaned-up version]

**Answer:** [the synthesis]

## Sources

- [[article-slug]] — [one-line relevance]
- Observation [slug] — [one-line relevance]
```

Add the filed query to INDEX.md under `## Queries`.

## Edge cases

- **No matching articles:** fall back to recent observations. If still nothing: "I do not have anything on that in the wiki yet. Want to save a first observation about it?"
- **Wiki is empty:** "The wiki is empty. Drop some observations first with `save:` and I will start finding patterns."
- **Query is actually a new observation:** if the user asks something like "I noticed X at work today, have I seen this before?" — answer the question AND offer to save the new observation.
- **Query spans 5+ articles:** the answer may be too broad. Offer to narrow: "This touches a lot of ground. Want me to focus on [specific angle]?"
- **Entity query with no page:** "I do not have a page for [name] yet. Want me to create a stub and log any observations about them?"

## Self-observation triggers

Log to CHANGELOG.md if:
- The user asked about a topic the wiki should cover but does not (compilation gap)
- A query revealed the wiki was wrong or out of date
- A query answer required reading 5+ articles (maybe the wiki needs better cross-linking)
- Filed query significantly overlaps with an existing concept article (might need merging)
