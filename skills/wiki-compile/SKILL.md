---
name: marcus-wiki-compile
description: "Cluster unprocessed observations into patterns, revise articles, flag contradictions"
tags: [wiki, compile, patterns, learning]
user-invocable: true
metadata:
  openclaw:
    emoji: "🔍"
---

# Wiki Compile Skill

## When to trigger

- 5 or more unprocessed observations (`processed: false` in frontmatter)
- Manual: the user says "compile", "find patterns", "compile observations", "update wiki"
- Max one compile per day unless the user forces it

Do NOT auto-run. Even when the threshold is hit, ask first.

## What to read

| File | Why |
|---|---|
| All `wiki/observations/*.md` with `processed: false` | Source material |
| `wiki/log.md` | Last compile timestamp, context of recent activity |
| `wiki/INDEX.md` | Current article inventory |
| Existing `wiki/concepts/*.md` | Articles that might need updating |
| Existing `wiki/entities/*/*.md` | Entity pages that might need updating |
| `wiki/briefs/*.md` | Look up brief parents when flagging single-event grounding |

## What to write

| File | When |
|---|---|
| `wiki/concepts/[slug].md` | New concept article (after the user confirms), or revised existing |
| `wiki/entities/*/*.md` | Updated with new observation references |
| `wiki/observations/*.md` | Flip `processed: false` to `true`, add `cluster:` field |
| `wiki/INDEX.md` | After any article creation or update |
| `wiki/log.md` | Append compile entry, contradiction entries, rewrite entries |

## Behavior

### Step 1 — Scan unprocessed observations

Read all files in `wiki/observations/` with `processed: false`. If fewer than 3 AND not manually triggered, exit silently.

### Step 2 — Cluster

**Articulate each claim before grouping.** Before grouping anything, produce a flat written list mapping every unprocessed observation slug to a one-line articulation of the claim that observation makes in isolation. Hold this list as a visible artifact throughout the clustering decision — it is not a mental step. Cluster by comparing articulation strings to candidate cluster syntheses, not by matching atom titles, file bodies, domains, or shared vocabulary. An atom whose articulation does not match a candidate synthesis does not join the cluster, regardless of shared vocabulary or domain.

With that list in hand, group observations by topic, entity, or heuristic pattern. Use your judgment. Clusters can be:
- **Entity-based:** multiple observations about the same person or company
- **Topic-based:** multiple observations about the same kind of situation (giving feedback, running meetings, handling disagreement)
- **Pattern-based:** observations that seem to point at the same underlying rule ("X works when Y but not Z")

A cluster of 1 is not a cluster. Leave singletons unprocessed for now; they may cluster later.

### Step 3 — For each cluster of 3+, decide create / extend / revise

**Existing concept article covers this cluster?**
- Read it. Merge the new observations into the existing article. **Rewriting sections is allowed and expected** when the new material genuinely changes the synthesis. Do not just append bullets. Preserve any section tagged `<!-- user-framing: true -->` verbatim.
- Update `updated` date and `sources` list.
- If the rewrite changed meaning significantly, log it: `## [YYYY-MM-DD] rewrite | [slug] | [one-line what changed]`

**No existing article for this cluster?**
- Do NOT auto-file.
- **Preamble — once per compile, before the first cluster proposal.** Output a flat list titled `Articulations from this compile:` that names every unprocessed atom slug in the current compile run paired with its one-line articulation from Step 2. Include atoms that did not cluster — unclustered atoms are exactly the ones the user most needs to see, because under-clustering is half the failure mode this preamble targets. Output this list once per compile, not once per cluster. With the articulations on the page, the per-cluster proposal block below stays terse: the reader already has the articulations and does not need them repeated per proposal.
- Before presenting any proposal, attempt to name at least one honest edge where the pattern does not hold; if no edge surfaces, include a visible `no edge found` flag inside the proposal itself so the user sees the gap before confirming the article.
- **Propose the pattern:**
  > "I am seeing a pattern across these observations:
  > - [obs 1 slug]
  > - [obs 2 slug]
  > - [obs 3 slug]
  >
  > It looks like: [your synthesis in 2-3 sentences].
  >
  > Edge: [one honest edge where it does not hold] — or `no edge found — may not yet be a pattern`.
  >
  > Should I file this as a concept article called `[proposed-slug]`?"
- Only create the article if the user confirms. If they reject or want changes, adjust.

**Single-event grounding check.**

Before presenting a pattern proposal, check whether 3+ of the supporting observations in the cluster share the same `brief:` frontmatter parent. If yes, flag the proposal explicitly:

> "All [N] supporting atoms came from the same brief ([brief-slug]). This pattern has single-event grounding and may need cross-event confirmation before filing."

The flag is informational, not blocking. The user decides whether to proceed. Log the single-event grounding in the compile summary.

**Unknown-grounding check.**

Parallel to the single-event-grounding check. Before presenting a pattern proposal, read each supporting atom's `provenance:` frontmatter. If 2+ supporting atoms are `provenance: unknown`, flag the proposal:

> "[N] of the [M] supporting atoms in this cluster are `provenance: unknown` — the user cannot confirm whether these claims are their own or echoed from something they read. Grounding may not be load-bearing."

Informational, not blocking. Same shape as the single-event-grounding flag. Log in the compile summary.

**Evidence-bullet prefix by provenance.** When writing or revising evidence bullets in Step 3 create/extend, read each atom's `provenance:` field and emit the correct prefix:

- `provenance: lived` → `[Experience, YYYY-MM-DD]`
- `provenance: imported` → `[Reading, <source_ref>, YYYY-MM-DD]` — use the atom's own `source_ref` value, not a guess
- `provenance: unknown` → `[Unknown, YYYY-MM-DD]`

Brief-originated atoms keep the extended `from brief brief-slug` suffix and compose with the new prefixes — e.g. `[Reading, a panelist, 2026-04-09, from brief 2026-04-09-a-workshop-event]`. `sources_internal:` still lists atom slugs regardless of provenance, because `sources_internal:` is curation-based (see AGENTS.md section 6).

### Step 4 — Flag contradictions

For each new observation, check whether it contradicts a claim in an existing article. If yes:
1. Do NOT overwrite the old claim.
2. Add (or extend) a `## Contradictions` section in the affected article:
   ```markdown
   ## Contradictions

   - **[YYYY-MM-DD]** Observation `[slug]` suggests [new finding], which contradicts the earlier claim that [old claim]. Needs review.
   ```
3. Log it: `## [YYYY-MM-DD] contradiction | [article-slug] | [one-line]`
4. Mention contradictions explicitly in the compile summary.

### Step 5 — Update entity pages

For each observation referencing an entity, make sure the entity page lists the observation under `## Mentions` with date and slug. If the observation reveals new information about the entity (role change, new pattern of behavior), update the relevant section of the entity page.

### Step 6 — Mark observations processed

For every observation you clustered, edit its frontmatter:
```yaml
processed: true
cluster: [concept-slug or entity-slug]
```

Leave unclustered observations as `processed: false` — they will wait for more material.

### Step 7 — Update INDEX.md

Add new articles to the appropriate category. Update "Recently Updated" with the last 10 article changes.

### Step 8 — Log the compile

Append to `wiki/log.md`:
```
## [YYYY-MM-DD] compile | [N observations processed, M articles touched, K patterns proposed, L contradictions]
```

### Step 9 — Summarize

Short summary of what happened:
- Observations processed: N
- Articles created: [list of slugs]
- Articles revised: [list with one-line what changed]
- Contradictions flagged: [list]
- Patterns proposed but waiting on confirmation: [list]
- Observations still unclustered: count
- New concept articles filed without a named edge: count (report 0 if none — do not suppress the line)

Ask: "Anything you want to teach-back?" to offer the next step naturally.

## Article format

New concept articles use this template:

```markdown
---
title: [Article Title]
type: concept
created: YYYY-MM-DD
updated: YYYY-MM-DD
sources_internal: [list of observation slugs]
sources_external: [list of external references, e.g. "Cagan 2017 — Inspired", empty if none]
tags: [relevant tags]
---

# [Article Title]

**Summary:** [2-3 sentences. The core pattern in plain language.]

## Pattern

[The heuristic itself. When it applies. What triggers it.]

## Evidence

[Brief reference to each source observation with date and one-line summary. Wikilinks to the observation files.]

## Edges and exceptions

[Required: name at least one honest edge before filing — a condition, person, or situation where the pattern does not hold. If no edge can be named, the cluster may not yet be a pattern; reconsider the proposal rather than fabricate an edge. A visible "no edge found" flag on the proposal is a stronger diagnostic signal than a weak edge invented to satisfy the rule.]

## Related

[[other-concept-slug]] — [one-line relationship]
[[entities/people/some-person]] — [one-line relationship]
```

Evidence bullets for atoms extracted from a brief use the extended prefix:

```
- [Experience, YYYY-MM-DD, from brief brief-slug] — [atom summary]
```

Atoms not from a brief keep the standard `[Experience, YYYY-MM-DD]` prefix. `sources_internal:` lists atom slugs only, not brief slugs. Brief context lives in the prefix prose.

The prefix list supports four shapes now that provenance is tracked per atom:

- `[Experience, YYYY-MM-DD]` — lived atom, not from a brief
- `[Experience, YYYY-MM-DD, from brief brief-slug]` — lived atom, from a brief
- `[Reading, source_ref, YYYY-MM-DD]` — imported observation (observation-sourced import maps to `[Reading, ...]`, never `[Imported]`)
- `[Reading, source_ref, YYYY-MM-DD, from brief brief-slug]` — imported atom inside a brief
- `[Unknown, YYYY-MM-DD]` — `provenance: unknown` atom, degenerate form of `[Experience, ...]`

`[Research, source-name]` remains reserved for research-note provenance sourced through wiki-ingest or a direct research note, not for observation-sourced imports.

## Edge cases

- **No existing articles (first real compile):** every cluster becomes a proposal. Expect a conversation.
- **All observations in one giant cluster:** break it into sub-clusters by theme. One mega-article is less useful than three focused ones.
- **A cluster contradicts itself internally:** file it anyway but with a `## Contradictions` section noting that the observations themselves disagree. This is important signal.
- **An observation has no natural cluster:** leave it. Do not force-fit.
- **User rejects a proposed pattern:** log it in CHANGELOG.md so Marcus learns not to propose that shape again. Do not re-propose the same pattern on the next compile.

## Self-observation triggers

Log to CHANGELOG.md if:
- A proposed pattern was rejected (what did the user dislike about it?)
- A rewrite significantly changed an article's meaning
- A contradiction keeps getting flagged without resolution (same article flagged 3+ times)
- Clustering produced groupings that surprised the user
- A concept article was filed without a named edge (repeated edge-absence across compiles signals drift toward fabricated or hollow patterns)
- A cluster dominated by `provenance: imported` or `provenance: unknown` evidence was filed as a concept article. This may signal the wiki is drifting toward imported-synthesis — concepts built from things the user read about rather than patterns they lived through. One instance is not enough to act on; track it and watch for repetition.
