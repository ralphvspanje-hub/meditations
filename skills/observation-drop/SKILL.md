---
name: marcus-observation-drop
description: "Capture a work or life observation into the Meditations wiki"
tags: [capture, observation, wiki]
user-invocable: true
metadata:
  openclaw:
    emoji: "📝"
---

# Observation Drop Skill

## When to trigger

**Explicit trigger:** the user uses the word `save`.

Examples:
- "save: today the standup ran better when I shared context first, then asked for input"
- "save this — feedback sandwich backfired with a teammate, they prefer direct"
- "save [long observation text]"
- "save" as a reply to a previous message the user wants captured

**Proactive trigger:** during normal conversation, if the user mentions something that sounds like an observation (a workplace pattern, a thing that worked or did not, a heuristic they noticed, a specific interaction with a named person or organization) and they did NOT use the `save` prefix, ask ONCE:

> "That sounds like something worth saving. Want me to log it as an observation?"

If yes, capture it. If no or the user changes the subject, drop it and do not re-ask in the same turn. Never nag. Never ask twice in a row.

**Do NOT trigger on:**
- Casual uses of "save" (like "how do I save a file")
- General chit-chat that happens to mention work topics
- Questions (those route to wiki-query)

## What to read

| File | Why |
|---|---|
| `wiki/INDEX.md` | Check if any mentioned entities already exist |
| `wiki/entities/` | If a name is mentioned, look up the existing person/company/project page |
| `wiki/log.md` | Check recent entries to avoid duplicate captures |

## What to write

| File | When |
|---|---|
| `wiki/observations/YYYY-MM-DD-[slug].md` | Every save — one file per observation |
| `wiki/entities/[category]/[slug].md` | Create or update if a new or existing entity is referenced |
| `wiki/log.md` | Append a `save` entry |

## Behavior

1. **Parse the content.** Extract: the observation itself (preserve the user's phrasing), the date (default today), any named entities (people, companies, projects), rough topic tags. If the observation describes the user's own experience, decision, skill, or lesson learned, include `me` in entities. Most observations will include `me` — the user is the subject of their own wiki. Omit only when the observation is purely about an external fact or another person's idea with no connection to the user's own experience.

   - **Length and claim check.** If the content is longer than 200 words (word count, not character count), offer brief mode once:

     > "This looks like a brief with multiple claims. File as a brief with extracted atoms, or as one observation?"

     If the user accepts, hand off to brief-drop (invoke the brief-drop skill with the same content). If they decline or do not engage, proceed as a single observation. Do not ask twice. Length-only threshold, no LLM-judgment layer.

   - **Unknown-provenance cue detection.** After parsing and the length check, scan the observation body for all three of the following cues. If and only if all three hold, fire the one-shot provenance prompt below. The cue list is deliberately narrow and tuned for a high false-negative rate — most imported claims will not trip it, and that is intended. The prompt exists to catch the loud cases without pestering the user on every save.

     (a) Third-person heuristic register. Examples: "a PM who...", "good founders...", "the best designers...", "people who...". The observation states a rule about a class of people rather than about the user.

     (b) Explicit attribution phrasing. Examples: "X said", a named author, a named speaker, a named publication, or a named podcast/newsletter in the body.

     (c) First-person absent across the entire body. No "I", "me", "my", or "we" anywhere in the observation text (not counting the frontmatter or the auto-generated `**Source:**` line).

     If all three hold, ask exactly once:

     > "This looks imported rather than lived. Is it? If yes, give me a rough source_ref (one word is fine)."

     If the user answers yes with a source_ref, set `provenance: imported` and `source_ref:` on the observation. If they say no, set `provenance: lived`. If they decline to answer or change the subject, default to `lived` and do not re-ask. No second prompt, no follow-up.

   - **Typo-fix pass.** Before writing the observation file, run a light spelling correction over the observation body (not the frontmatter).
     - Allowed: fix obvious single-word typos where one clearly intended word exists (e.g. `ervything` → `everything`, `teh` → `the`, `recieve` → `receive`, `alot` → `a lot`).
     - Forbidden: changing word choice, phrasing, grammar constructions, verb tense, register, or any stylistic choice. If unsure whether a word is a typo or an intentional choice, leave it.
     - Never touch: proper nouns, brand names, technical terms, non-English words, domain names, code identifiers. When in doubt, skip.
     - One-shot, no per-fix approval prompts. Silent unless fixes were made.
     - After writing the file, the Step 5 acknowledgement must include a diff line when fixes were applied: `Fixed N typos: ervything → everything, teh → the.` If zero fixes, omit the line.

2. **Handle named entities.**
   - If a person is named and no entity page exists, create `wiki/entities/people/[slug].md` with a minimal frontmatter stub and a first mention note. If the name is ambiguous (common first name, no surname), ask the user once for clarification before creating.
   - If a company or project is mentioned, same treatment.
   - If the entity already exists, append a line under a `## Mentions` section referencing the new observation by date.

3. **Write the observation file** to `wiki/observations/YYYY-MM-DD-[slug].md`. Slug is a 3-5 word lowercase-hyphenated summary. Use this format:

```markdown
---
date: YYYY-MM-DD
entities: [list of entity slugs, or empty]
tags: [rough topic tags]
processed: false
provenance: lived
source_ref: ""
---

# [One-line title capturing the observation]

[The observation itself, in the user's own phrasing when possible. Preserve their language. If they wrote a long dump, keep it verbatim. If they said it in conversation, capture the essence in 2-4 sentences.]

**Source:** [where the observation came from: a meeting, a 1:1, a project, a personal reflection, etc. If unclear, omit.]
```

Provenance defaults to `lived` with an empty `source_ref:`. If the cue-detection sub-step in Step 1 fired and the user confirmed import, set `provenance: imported` and populate `source_ref:` with their one-word answer. If they declined to answer, leave the defaults. Omit `source_ref:` entirely only if you want a clean frontmatter on a lived observation — an empty string is also valid.

4. **Append to `wiki/log.md`:**
```
## [YYYY-MM-DD] save | [slug] — [one-line summary]
```

5. **Acknowledge briefly.** One line, calm tone:
> "Saved. [short recap.] [N] observations unprocessed."

If the typo-fix pass in Step 1 applied any fixes, append a diff line: `Fixed N typos: ervything → everything, teh → the.` If zero fixes, omit the line.

Do not summarize back the whole observation. Do not interpret it yet. Interpretation happens in compile.

6. **Check compile threshold.** Count unprocessed observation files (frontmatter `processed: false`). If 5 or more, mention it once:
> "You have 5 unprocessed observations. Want me to compile them into patterns?"

Do not auto-run compile without the user's confirmation.

## Entry format details

- **Slug examples:** `2026-04-05-standup-context-first.md`, `2026-04-07-teammate-direct-feedback.md`, `2026-04-10-retro-surfaced-honesty.md`
- **Preserve phrasing:** if the user says "the vibe was weird," do not rewrite to "the atmosphere was unusual." Their words, their framing.
- **Short is fine:** a 2-sentence observation is a valid observation. Do not pad.

## Edge cases

- **User says "save" with no content:** ask "What do you want to save?"
- **User pastes a long message with "save":** capture it verbatim, do not summarize. You can add a one-line title at the top.
- **Duplicate observation:** if a very similar one exists from the last 7 days, mention it: "You saved something similar on [date]. Want me to add this as a new one, or merge into that?"
- **Observation with no clear topic:** still save it. Tag as `uncategorized`. Compile will figure out clustering later.
- **Observation names a person Marcus does not know:** create the person page stub. Ask the user once for context ("Who is this? One line of context helps me link future observations.") but do not block the save on it.
- **User pastes a long multi-claim dump with `save:`:** offer brief mode once per the 200-word rule above. Do not silently reroute. The user decides.

## Self-observation triggers

Append to CHANGELOG.md if:
- The user corrects how you captured an observation (learn their preferences)
- A proactive trigger was rejected (learn what does NOT count as save-worthy to the user)
- An observation did not fit any existing entity or topic cleanly (might indicate a new category)
- If the user consistently accepts the brief-mode offer when it fires, consider lowering the 200-word threshold. If they consistently decline, consider raising it or removing the offer.
- If the same miscapture shape repeats 3+ times — an imported claim was filed as `lived` because the cue-detection sub-step did not trip — the cue list is too narrow and should be widened. One data point is not enough; wait for a third symptom before revisiting the cues.
- If the user pushes back on a typo fix (says "that was intentional" or "put it back"), log it in CHANGELOG.md. Three pushbacks across different saves means the rules are too loose — tighten.
