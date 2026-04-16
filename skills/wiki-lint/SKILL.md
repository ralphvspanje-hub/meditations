---
name: marcus-wiki-lint
description: "Health-check the wiki for broken links, stale content, frontmatter drift, and structural issues"
tags: [wiki, lint, health-check, audit]
user-invocable: true
metadata:
  openclaw:
    emoji: "🩺"
---

# Wiki Lint Skill

## When to trigger

**Manual only.** Never auto-triggered. Marcus does not nag about wiki health.

- `lint`, `lint wiki`, `health-check`, `audit wiki` — full-wiki run
- `lint [slug]` or `audit [slug]` — single-page run (e.g. `lint me`, `audit acme-corp`)
- `lint concepts`, `lint entities`, `lint observations` — category run

Do NOT trigger on casual mentions of "lint" in conversation about other projects.

## What to read

| File | Why |
|---|---|
| `wiki/INDEX.md` | Cross-check against disk, find all page paths |
| All `wiki/concepts/*.md` | Frontmatter, wikilinks, sections |
| All `wiki/entities/**/*.md` | Frontmatter, Mentions section, wikilinks |
| All `wiki/observations/*.md` | `processed` field, age, entity references |
| All `wiki/sources/*.md` | Frontmatter conformance |
| All `wiki/briefs/*.md` | Frontmatter conformance, atom backlink integrity |
| All `wiki/queries/*.md` | Frontmatter conformance |
| `wiki/log.md` | Last lint timestamp |
| `AGENTS.md` section 6 | Canonical frontmatter spec to check against |

For single-page runs, read only the target page plus INDEX.md and AGENTS.md section 6.

## What to write

| File | When |
|---|---|
| `wiki/queries/YYYY-MM-DD-lint-[scope].md` | Only if the user says "file it" after seeing the report |
| `wiki/log.md` | Append a `lint` entry every run |

The lint never modifies wiki content. Report only.

## Check catalog

Run these checks in order. Skip checks that do not apply to the current scope.

### 1. Frontmatter conformance

Check against AGENTS.md section 6:
- Required fields present (`title`, `type`, `created`, `updated` for articles; `date`, `entities`, `tags`, `processed` for observations)
- No flat `sources:` field — must be split into `sources_internal:` / `sources_external:`
- `origin:` present on entity pages, absent on concept pages
- `origin:` value is one of `experienced`, `research`, `reference`
- `type:` value matches the file's location (concept in `concepts/`, entity type in `entities/`)
- For briefs: required fields are `title`, `type: brief`, `date`, `updated`, `entities`, `tags`, `atoms`. `type: brief` files must live under `wiki/briefs/`.
- `provenance:` field is required on every observation and every brief. Missing field is a lint error.
- `provenance:` value must be one of `lived | imported | unknown`. Any other value is a lint error.
- `source_ref:` is required when `provenance: imported`. An imported observation or brief with an empty or missing `source_ref:` is a lint error.
- `source_ref:` is allowed on any provenance value. A `lived` observation with a populated `source_ref:` is not an error — lived observations may cite a source casually (for example, a tool or article the user happens to mention while describing their own experience).

### 2. Broken wikilinks

Scan all `[[slug]]` and `[[path/slug]]` references. Flag any that do not resolve to an existing file in `wiki/`.

### 3. Orphan pages

Wiki pages (concepts, entities, sources) with zero inbound wikilinks from other wiki pages. Observations are inherently leaf nodes and excluded from orphan detection. Briefs with an empty `atoms:` list are valid (pure-narrative briefs) and excluded from orphan detection as long as they have inbound wikilinks from entity Mentions.

### 4. Missing concept pages

Scan entity and concept pages for topics mentioned 3+ times across different pages that lack their own concept article. Use tag frequency and repeated phrases as signals.

### 5. Stale content

Entity or concept pages where `updated:` is more than 30 days old AND newer observations reference the same entities or tags.

### 6. Unresolved contradictions

`## Contradictions` sections that have been open for 14+ days without resolution.

### 7. Old unprocessed observations

Observations with `processed: false` that are older than 14 days.

### 8. Entity pages missing Mentions

Entity pages that lack a `## Mentions` section entirely.

### 9. INDEX.md drift

- Pages on disk not listed in INDEX.md
- INDEX.md entries pointing to nonexistent files

### 10. Empty sections

Article sections (## headings) that contain only a heading and no content below them.

### 11. Atom/brief backlink integrity

- Each brief's `atoms:` list must resolve to real observation files. Missing files are broken links.
- Each observation with a non-empty `brief:` frontmatter field must reference a real brief file in `wiki/briefs/`. Orphaned backlinks are lint findings.
- Each brief body must contain an `## Atoms` section. If `atoms:` is empty, the section reads `_No atoms extracted from this brief._`. If `atoms:` is non-empty, the section must contain body wikilinks to every atom in the list.
- Each atom whose `brief:` field is non-empty must contain a body wikilink `_From [[briefs/...]]_` near the top. Frontmatter alone does not create graph edges.

## Behavior

### Step 1 — Determine scope

Parse the trigger to decide: full wiki, single category, or single page. For single-page runs, resolve the slug to a file path. If the slug is ambiguous (e.g. `lint alex` could match multiple files), ask the user to clarify.

### Step 2 — Read pages in scope

Read all files in scope. For full-wiki runs, start with INDEX.md, then read each category in order: concepts, entities, observations, sources, briefs, queries.

### Step 3 — Run checks

Run each check from the catalog against the pages in scope. Collect findings. Each finding is a tuple: (check name, file path, one-line description, one-line fix suggestion).

For single-page runs, skip wiki-wide checks (orphan pages, missing concept pages, INDEX drift). Only run checks relevant to the page type.

### Step 4 — Build the report

Group findings by check category. Count totals. Format:

```
## Lint report — [scope] — [date]

### Issues ([N] total)

**[Check name]** ([n])
- [file] — [description]. Fix: [suggestion].
- ...

### Summary

[M] pages checked, [K] passed all checks.
```

If no issues found: "All [M] pages pass. Wiki is clean."

### Step 5 — Present to the user

Show the report in chat. If 5+ findings, offer to file:

> "Want me to file this report to `queries/` so you can track the fixes?"

For small reports (under 5 findings), do not offer — that would be noise.

### Step 6 — Log the lint

Append to `wiki/log.md`:

```
## [YYYY-MM-DD] lint | [scope] — [N issues across M pages]
```

## Filing a lint report

When the user agrees to file, write `wiki/queries/YYYY-MM-DD-lint-[scope].md`:

```markdown
---
date: YYYY-MM-DD
question: "Wiki lint — [scope]"
sources_internal: []
sources_external: []
tags: [lint, health-check]
---

# Lint report — [scope]

[The full report as shown in chat]
```

Add to INDEX.md under `## Queries`.

## Edge cases

- **Empty wiki:** "Nothing to lint yet. Drop some observations first."
- **Single-page audit, page not found:** "No page matching `[slug]`. Check the slug or try `lint wiki` for a full run."
- **Lint finds nothing:** "All [M] pages pass. Wiki is clean." Log the lint anyway.
- **Contradiction already flagged:** Report it with "(open since [date])" — do not double-flag. Visibility is the point.
- **User asks to fix something:** Offer to run the relevant skill (compile for unprocessed observations, observation-drop for missing entity mentions, manual edit for frontmatter). Lint does not auto-fix.
- **Back-to-back lint runs same day:** Run it. Do not block. Some audits are iterative (fix, re-lint, fix, re-lint).

## Self-observation triggers

Append to CHANGELOG.md if:
- The same finding appears on 3+ consecutive lint runs (something is not getting fixed — worth flagging)
- A check consistently produces zero findings across 5+ runs (the check may be retired or its threshold adjusted)
- The user asks for a check that does not exist in the catalog (add it)
