# Obsidian graph view colors

Canonical documentation of the four-color palette Meditations uses in Obsidian's graph view.

`.obsidian/graph.json` is not tracked in git because Obsidian rewrites it on every graph view interaction (zoom, pan, format drift). Color groups are durable config; view state is volatile. Rather than track a noisy file, the color scheme lives here as plain-text documentation that any agent can read without Obsidian running, following Farzapedia's Explicit principle.

## The palette

Four content layers colored, reference types (sources, queries) stay neutral.

| Node type | Path query | Hex | Decimal RGB | Semantic role |
|---|---|---|---|---|
| Concepts | `path:wiki/concepts` | `#a855f7` | `11032055` | Synthesis layer — patterns compiled from observations |
| Observations | `path:wiki/observations` | `#10b981` | `1096065` | Raw atomic claims from lived experience |
| Briefs | `path:wiki/briefs` | `#f59e0b` | `16096779` | Narrative captures of rich multi-claim events |
| Entities | `path:wiki/entities` | `#f472b6` | `16020150` | People, companies, projects (reference anchors) |

Obsidian stores `color.rgb` as a decimal integer in `.obsidian/graph.json`. The hex column is for human reading and for reconstructing the palette in any tool that speaks standard color notation.

## How to apply on a fresh machine

1. Open the Meditations vault in Obsidian.
2. Open graph view (`Ctrl+G` or `Cmd+G`).
3. Click the settings gear icon in the graph view's top-right.
4. Expand the "Groups" section.
5. For each row in the table above, click `+ New group`:
   - Paste the `path:...` query into the query field.
   - Click the color swatch and enter the hex code from the table.
6. Close the settings panel. Colors apply immediately.

Takes about two minutes.

## Why briefs are amber and observations are emerald

The palette was chosen to carry semantic meaning:

- **Purple concepts** sit near the default link-edge color on the color wheel, so synthesized wisdom visually continues the connection theme.
- **Emerald observations** evoke raw growth and freshness — atomic claims are the raw material the wiki compounds from.
- **Amber briefs** are warm and event-like, standing out against the cooler observations as hubs where narrative gets captured.
- **Rose entities** fill the gap between purple and amber on the wheel, staying distinct from both while carrying a "people and relationships" connotation.

The cooler three (purple, emerald, rose) and the warm one (amber) form a triadic-plus-one scheme that pops against each other without fighting.

## History

Palette introduced in commit `3c890a1` (2026-04-09) as part of the brief-drop skill landing. Originally tracked via a `.gitignore` whitelist for `.obsidian/graph.json`, but Obsidian's rewriting of the file on every graph interaction produced constant `git status` noise. Untracked and documented here in a follow-up cleanup commit.
