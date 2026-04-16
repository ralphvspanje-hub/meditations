# Source summary pages

This directory holds LLM-written summary pages — one per ingested raw source.

Each file in here corresponds to exactly one file in [`../../raw/`](../../raw/). The relationship is 1:1. The raw file is the immutable ground truth; the summary page is Marcus's view of it, shaped by the discussion step in the ingest skill.

## Frontmatter convention

```yaml
---
title: [Source title]
type: source-summary
created: YYYY-MM-DD
updated: YYYY-MM-DD
author: [author or organization]
citation: [relative path to the raw file]
sources_internal: [observation slugs that reference this source, if any]
tags: [topic tags]
---
```

`type: source-summary` is the marker that tells any agent (or Dataview query) this is a summary page, not a concept article or an entity page.

`citation:` always points at the raw file. Do not duplicate raw content here — link to it.

## Body structure

See [skills/wiki-ingest/SKILL.md](../../skills/wiki-ingest/SKILL.md) Step 3 for the full template. Short version: key ideas, notable quotes, applied to the user's context, open questions, related links. Capped under ~300 lines.

## Internal by directory

Files in `wiki/observations/` are inherently internal (your own experience). Files in `wiki/sources/` are inherently external (imported reading). Location tells you which mode a record is in — no extra tag needed. See AGENTS.md section 6, "Internal vs external sources."
