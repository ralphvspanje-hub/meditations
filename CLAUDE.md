# Marcus

Read [AGENTS.md](AGENTS.md) for everything. This agent is self-contained.

## Hard rules for any session entering this folder

**1. Auto-memory is disabled in this project.**

Do NOT use the Claude Code auto-memory system (`~/.claude/projects/.../memory/`) for anything in `Meditations/`. All persistence in this project lives in files inside `Meditations/` itself, where the user can see and curate it. Hidden harness state violates the Explicit and Yours principles. Every persistence operation in this project must land in a markdown file the user can see and edit.

**2. `save:` always means Marcus observation-drop.**

When the user types `save:` (or "Save:") followed by any content in this folder, it ALWAYS invokes Marcus's observation-drop skill at [skills/observation-drop/SKILL.md](skills/observation-drop/SKILL.md). It never triggers auto-memory. It never triggers a harness-level "save to memory" action. Read [AGENTS.md](AGENTS.md) and [skills/observation-drop/SKILL.md](skills/observation-drop/SKILL.md) before interpreting the command, then write the observation file to `wiki/observations/`, update any mentioned entity pages, and append to `wiki/log.md`.

Observation-drop may offer brief mode once when save: content is longer than 200 words and appears multi-claim. If the user accepts, observation-drop hands off to brief-drop. If the user declines or does not engage, save proceeds as a single observation. The default is file-as-one.

**3. If in doubt, read AGENTS.md first.**

Before acting on any command in this folder that looks like a persistence operation (save, remember, note, log, etc.), read [AGENTS.md](AGENTS.md) section 7 (the skills table) to see which skill the command maps to. Never guess, never fall back to harness-level memory.

**4. `brief:` always means Marcus brief-drop.**

When the user types `brief:` (or "Brief:") followed by content in this folder, it ALWAYS invokes the brief-drop skill at [skills/brief-drop/SKILL.md](skills/brief-drop/SKILL.md). It never routes to observation-drop. It never triggers auto-memory. It never collapses into a single observation. Brief-drop writes the verbatim dump to `wiki/briefs/` and files each approved atom to `wiki/observations/` with the `brief:` backlink populated, following the blanket-brief-plus-specific-atom Mentions rule in [AGENTS.md](AGENTS.md) section 6.

**5. Superpowers skill discovery is disabled in this folder.**

Marcus is self-contained by design (BYOAI: bring your own AI — the schema and skills must work without dependencies outside this folder). The trigger words `save:`, `brief:`, `ingest:`, `compile`, `wiki:`, `lint`, etc. route directly to the right Marcus skill via the rules above and via [AGENTS.md](AGENTS.md) section 7. The `superpowers:using-superpowers` skill provides no value here — every Marcus skill is already discovered by its trigger word.

Do NOT invoke `superpowers:using-superpowers` before responding to a Marcus operation in this folder. Do NOT invoke any other `superpowers:*` skill unless the user explicitly names it. Skip the skill-discovery step entirely and go straight to reading [AGENTS.md](AGENTS.md) and the relevant Marcus skill file.

This rule overrides the default superpowers framework instructions per its own override hierarchy ("User's explicit instructions in CLAUDE.md, GEMINI.md, AGENTS.md, or direct requests — highest priority"). Saves tokens on every Marcus session and removes a class of skill-discovery overhead that does not apply here.

**6. First-run onboarding is required.**

On any new session, if `wiki/entities/me.md` does not exist, the first action is to invoke `skills/onboarding/SKILL.md` before any other skill or response. This creates the user's self entity page, which the rest of the wiki depends on.
