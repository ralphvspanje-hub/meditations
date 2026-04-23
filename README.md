# Meditations

An AI agent that turns your scattered observations, books, and podcasts into a living knowledge graph you actually use.

Named after Marcus Aurelius, who kept his notes for himself and somehow we're still reading them 1,800 years later. The agent inside is called Marcus, for the same reason.

---

## Why

I always wanted to journal for the active recall part. Writing things down is how you actually remember them.

But I never stuck with it, because looking back was useless. A journal is static. Scattered observations, no connections, nothing to pull from when you actually need it.

Meditations solves the retrieval half. You drop observations in plain English. Marcus files them, auto-links them to related notes, builds entity pages for every person you mention, and surfaces patterns over time. You can ask questions against your own history and he answers from it. He also pings you with old observations so nothing dies in an archive.

It is not a productivity tool. It is an external memory that happens to be written in your own words.

---

## Design principles

Four load-bearing decisions. Everything else is downstream.

- **File over app.** Plain markdown only. If any specific AI tool disappears tomorrow, the wiki is still yours, readable in any editor, queryable with standard Unix tools.
- **BYOAI (bring your own AI).** The schema and skills are self-contained. A fresh agent (any Claude version, Codex, OpenCode, any future LLM) can pick up maintenance with no external dependencies.
- **Explicit.** All state is visible and editable in plain-text files. No hidden harness memory, no vector DBs, no binary caches.
- **Yours.** The data lives locally, in your own repo, in a format you can read, edit, version, move, or throw away without permission.

Prior art: [Karpathy's "LLM Wiki" gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) appeared while this project was already in progress. It shaped some later decisions but did not originate the design.

---

## Prerequisites

- [Claude Code CLI](https://claude.com/claude-code). Required. The skills in this repo are Claude Code skills.
- [Obsidian](https://obsidian.md/). Optional but recommended. The graph view is where the "compounding knowledge base" thing stops being an abstraction and starts being a thing you can see.

---

## Install

```
git clone https://github.com/ralphvspanje-hub/meditations.git Meditations
cd Meditations
claude
```

Send any message. On first run Marcus notices `wiki/entities/me.md` is missing and runs the onboarding skill: four short questions (your name, your focus, what you want the wiki to help with, tone preference) and he writes your self page. You are now live.

For Obsidian graph colors, open the vault in Obsidian and follow [docs/obsidian-graph-colors.md](docs/obsidian-graph-colors.md). Two minutes.

### Running it from your phone

Marcus can run from Telegram, Discord, or iMessage via Claude Channels, and can push scheduled observations via Claude routines. Both depend on claude.ai login and some setup the repo does not ship. The honest version of the stack, including a reference coordinator agent to copy, is in [docs/phone-setup.md](docs/phone-setup.md).

---

## The eight operations

| Operation | Trigger | One-line |
|---|---|---|
| `wiki-ingest` | `ingest:` followed by a path to a file in `raw/` | Ingest a raw source into the wiki as a summary page plus updates across affected entity and concept pages |
| `observation-drop` | `save` (explicit), or Marcus proactively asks when something sounds save-worthy | Capture a new observation |
| `brief-drop` | `brief:` followed by a multi-claim dump, or offered by observation-drop for long save: inputs | Capture a narrative or catch-up dump verbatim and extract atom observations from it |
| `wiki-compile` | 5+ unprocessed observations, or manual `compile` | Cluster observations into patterns, revise articles, flag contradictions |
| `wiki-query` | `wiki:`, `look up:`, `what do I know about...`, `how have I handled...` | Answer a question using the wiki |
| `teach-back-pattern` | After compile proposes a pattern, or manual `teach me back X` | Stress-test a pattern using your own explanation |
| `weekly-reflection` | Sundays (opt-in), or manual `weekly reflection` | Light prompt to surface missed observations |
| `wiki-lint` | `lint`, `health-check`, `audit [slug]`, `lint [category]` | Health-check the wiki for broken links, stale content, and structural issues |

A ninth skill, `onboarding`, runs once on the first session to create `wiki/entities/me.md`. Each skill has a full behavior spec in [skills/](skills/). Triggers match [AGENTS.md](AGENTS.md) section 7 exactly.

---

## Use cases

### Work observations

```
save: standups run better when I share context before asking for input
save: asking "what would have to be true" before solutions changed the tone
save: giving direct feedback to a teammate worked, feedback sandwich did not
```

After a few weeks Marcus starts noticing patterns about your communication style and how different people respond.

### Interpersonal notes

```
save: a coworker prefers bullet summaries, not paragraphs
save: another teammate responds to direct, hates hedging
```

Marcus builds entity pages for each person in `wiki/entities/people/`. Over time these become a quiet reference for "how do I actually work with X" that you can query before any important 1:1.

### Reading and listening

```
ingest: raw/inspired-2017-cagan.pdf
```

Marcus reads, discusses key takeaways with you, writes a summary in `wiki/sources/`, and cross-links to your own observations. A principle from a book in February can link to a real observation you log in June. Reading compounds with experience instead of disappearing into a highlights file you never open. Same for podcasts, articles, lectures, or anything else you drop into `raw/`.

### Life heuristics

```
save: I think more clearly after a walk than after coffee
save: 7h sleep is the floor, below that my judgment drops noticeably
```

Not everything has to be about work. Personal heuristics compound the same way.

---

## Folder map

- `raw/`: immutable source files you drop in. Marcus reads but never modifies.
- `wiki/`: everything Marcus writes. `sources/` for ingest summaries, `observations/`, `concepts/`, `entities/`, `briefs/`, `queries/`, plus `INDEX.md` and `log.md`.
- `skills/`: the nine skill files Marcus follows (eight operations + onboarding).
- `docs/`: `COACH.md` (the planning-partner agent) and `obsidian-graph-colors.md`.

Full tree in [AGENTS.md](AGENTS.md) section 5.

---

## Coach

Meditations ships with a second agent, **Coach**, defined in [docs/COACH.md](docs/COACH.md). Coach is a planning partner: long conversation, stress-test your ideas, hand you an execution prompt. Marcus executes, Coach plans. Load COACH.md in a separate session when you want to think about what Meditations should do next instead of running Marcus in reactive mode.

---

## License

MIT. See [LICENSE](LICENSE).
