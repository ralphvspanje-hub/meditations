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

### Capturing via Cursor or VS Code

If you do most of your work in an IDE, the Claude Code extension turns the Cursor or VS Code chat panel into a Marcus session tied to your Meditations folder. All eight operations work. This is the recommended default for anyone without a 24/7 machine. The install steps and the fresh-window discipline are in [docs/cursor-setup.md](docs/cursor-setup.md). This is also where you run the setup walkthroughs below — paste any of those prompts into a fresh Claude Code chat panel in Cursor or VS Code and the walkthrough agent takes it from there.

### Setup walkthroughs

Prefer a guided conversation over reading docs? Paste one of these into a fresh Claude Code session opened in the Meditations folder. A walkthrough agent will ask what you have in place and take you step by step.

**Set up Cursor or VS Code as your daily driver:**

The recommended default when you are at a laptop: the IDE chat panel becomes a full Marcus session with file tree, diffs, and graph view visible while you capture and query.

````
Follow @docs/SETUP.md. I want to set up the Claude Code extension in Cursor or VS Code as my main way to use Marcus. Walk me through it.
````

**Set up a daily observation push to your phone:**

Cloud to phone. A scheduled job fires once a day, picks a random old observation, and DMs it to you. No laptop needs to be on. Fire-and-forget.

````
Follow @docs/SETUP.md. I want to set up the daily-observation routine so I get a random old observation pushed to my phone once a day. Walk me through what I need.
````

**Set up Claude Channels for full phone access:**

Phone to laptop. Any message you text your bot lands as a Marcus event, so `save:`, `brief:`, `wiki:`, `compile` all work from anywhere — as long as your laptop (or a dedicated always-on machine) stays on to receive the messages.

````
Follow @docs/SETUP.md. I want to set up Claude Channels so I can use Marcus from Telegram. Walk me through what I need, including whether I actually have the always-on machine this needs.
````

**Set up Obsidian graph colors:**

Visual polish for the graph view. Four colors so observations, concepts, briefs, and entities render distinctly. Two minutes.

````
Follow @docs/SETUP.md. Walk me through setting up the Obsidian graph view colors.
````

The two phone options are different directions, not competing paths. Run either alone, or both together once the prerequisites land.

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
- `docs/`: `COACH.md` (the planning-partner agent), `SETUP.md` (the onboarding guide agent), `phone-setup.md`, `cursor-setup.md`, and `obsidian-graph-colors.md`.

Full tree in [AGENTS.md](AGENTS.md) section 5.

---

## Agents

Meditations ships with three agents, each with one job.

- **Marcus** maintains the wiki day to day: captures observations, compiles patterns, answers queries, lints for broken links. [AGENTS.md](AGENTS.md) is the schema. This is the agent you run when you already have the repo set up and you want to use it.
- **Setup** walks a new user through one setup path (Cursor or VS Code extension, daily-observation routine, Claude Channels, Obsidian graph colors) and stops. [docs/SETUP.md](docs/SETUP.md) is the spec. The paste-in prompts under Install load it.
- **Coach** is a planning partner for changes to Meditations itself: long conversation, stress-test your ideas, hand you an execution prompt a fresh session can run. [docs/COACH.md](docs/COACH.md) is the spec. Load it in a separate session when you want to evolve the project rather than just run it.

Marcus executes, Setup onboards, Coach plans. One agent per session, no blending roles.

---

## License

MIT. See [LICENSE](LICENSE).
