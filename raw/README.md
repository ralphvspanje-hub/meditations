# Raw sources

This directory is **layer 1** of the Meditations wiki architecture: the immutable ground-truth source collection.

## What this is

A curated collection of source documents you have decided are worth ingesting into the wiki. Books, articles, PDFs, web clippings, images, data files — any format is fine.

## What goes in

- Books and book summaries (PDFs, markdown, text).
- Articles and blog posts (preferably clipped to markdown, e.g. via Obsidian Web Clipper).
- Research papers.
- Podcast notes, video transcripts.
- Screenshots and images where the image itself is the source.
- Anything else you want Marcus to read and integrate into the wiki.

## What does NOT go in

- Wiki articles (those live in `wiki/concepts/`, `wiki/entities/`).
- Observations (those live in `wiki/observations/` and are internal — your own experience, not imported material).
- Source summary pages written by Marcus (those live in `wiki/sources/`).
- Derived content of any kind. This layer is ground truth only.

## Filename convention (recommended, not enforced)

`YYYY-MM-DD-slug.ext` or a clear descriptive name. Not enforced — pick whatever helps you find the file later.

## The immutability rule

**Marcus reads raw files. Marcus never edits them.**

This is the core discipline of the layer. If a source turns out to be wrong, out of date, or contradicted by a newer source, that lives in the wiki layer — the summary page gets updated, a contradiction gets logged, a newer source gets ingested alongside. The raw file stays as it was, because it is the historical record of what was true at the time it was added.

If you need to correct a raw file, add a newer version as a separate file. Never overwrite.

## Obsidian graph view

`raw/` is included in the Obsidian graph view by design. Orphan raw files (files you dropped here but never asked Marcus to ingest) show up as disconnected nodes in the graph. That is signal, not noise — it shows you what you have queued for ingestion but have not yet processed.

## How ingest works

See [skills/wiki-ingest/SKILL.md](../skills/wiki-ingest/SKILL.md). Short version: you type `ingest: raw/<filename>` and Marcus reads the file, discusses key takeaways, writes a summary page in `wiki/sources/`, updates affected entity and concept pages, and logs the ingest.
