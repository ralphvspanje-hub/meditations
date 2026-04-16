---
name: marcus-onboarding
description: "Bootstrap a fresh Meditations wiki by asking four short questions and creating the self entity page"
tags: [bootstrap, onboarding, wiki, setup]
user-invocable: true
metadata:
  openclaw:
    emoji: "🌱"
---

# Onboarding Skill

## When to trigger

**Automatic trigger:** any first message from the user in a session when `wiki/entities/me.md` does not exist. This overrides every other trigger. Run onboarding first, then handle the original message (if it was a real intent) or let the user direct the next turn.

This behavior is mandated by [CLAUDE.md](../../CLAUDE.md) hard rule #6. The rest of the wiki depends on `wiki/entities/me.md` — observations attribute themselves to `me`, concepts cite `me` as subject, entity Mentions link to `me`. A wiki without a self page cannot compound.

**Manual trigger:** `onboard`, `onboarding`, `re-onboard`, `rewrite my self page` — run even if `me.md` already exists, after confirming the user wants to overwrite.

**Do NOT trigger on:**
- Sessions where `wiki/entities/me.md` already exists (unless the user explicitly asked to re-onboard).
- Subsequent messages in a session that already ran onboarding.

## What to read

| File | Why |
|---|---|
| `wiki/entities/me.md` | Existence check only. If present, skip onboarding. |
| [AGENTS.md](../../AGENTS.md) section 6 | Entity frontmatter schema |

Do not read anything else. Onboarding is a single-turn setup — keep it lean.

## What to write

| File | When |
|---|---|
| `wiki/entities/me.md` | Every successful onboarding — the self entity page |
| `wiki/log.md` | Append one `init` entry |
| `wiki/INDEX.md` | Add the new self page under `## Entities > People`, update the last-updated stamp |

## Behavior

### Step 1 — Ask four short questions, one at a time

Ask them conversationally, not as a checklist. Wait for each answer before asking the next one.

1. "What is your name?"
2. "What is your current role, focus, or area of interest?"
3. "What kinds of things do you want this wiki to help you think about? (work patterns, personal heuristics, reading, projects — any mix)"
4. "Is there any tone preference? (concise / warm / formal)"

Accept short answers. If the user gives a one-word reply, that is fine — do not probe for more. The self page is a living document and can be edited directly later.

If the user declines to answer a question ("skip", "no preference", "whatever"), record that as "unspecified" in the relevant section and move on.

### Step 2 — Write `wiki/entities/me.md`

Create the file with this frontmatter and body:

```markdown
---
title: [User's name]
type: entity-person
created: YYYY-MM-DD
updated: YYYY-MM-DD
origin: experienced
sources_internal: []
sources_external: []
tags: [self]
---

# [User's name]

## Snapshot

[One or two sentences combining name and role. "[Name], a [role/focus]." — keep it factual and short.]

## Current focus

[The user's answer to question 2, lightly formatted. If they gave multiple items, render as a short bullet list.]

## Interests

[The user's answer to question 3. Render as a short bullet list if multiple items, otherwise a one-line paragraph.]

## Tone notes

[The user's answer to question 4. One line. If "unspecified," write: "No preference stated — default calm and direct per AGENTS.md."]
```

Slug is `me.md` — not the user's name, not a personalized slug. The self entity page is always `wiki/entities/me.md` so that observations can reference `me` uniformly regardless of who is running the wiki.

### Step 3 — Append to `wiki/log.md`

```
## [YYYY-MM-DD] init | user onboarded — [slug: me] created
```

### Step 4 — Update `wiki/INDEX.md`

Under `## Entities > People`, replace the placeholder `_No people pages yet._` with a bullet pointing at the new page:

```
- [[entities/me]] — self
```

Update the `_Last updated: (not yet populated)_` line at the top to `_Last updated: YYYY-MM-DD_`.

### Step 5 — Acknowledge

One line, calm tone:

> "Onboarded. Your self page is at `wiki/entities/me.md`. You can edit it directly anytime. Drop your first observation with `save:` when ready."

Do not recap the four answers. Do not suggest a next action beyond the one-line prompt above. Do not offer to compile or lint or anything else — the wiki is empty, there is nothing to compile against.

## Edge cases

- **`wiki/entities/me.md` already exists.** Skip onboarding entirely unless the user explicitly asked to re-onboard. If they did, confirm once ("This will overwrite your existing self page. Proceed?") before writing.
- **User refuses to answer any questions.** File a minimal self page: name = "User" (or whatever they gave), all other sections = "unspecified." The file existing is what unblocks the rest of the wiki; the content can be edited later.
- **User gives very long answers.** Capture them. The self page is not size-limited. But do not ask follow-up questions to extract more — onboarding is single-turn by design.
- **User starts with `save:`, `brief:`, or another trigger before onboarding.** Run onboarding first, then run the requested skill with the original content.
- **User's name contains characters that would be awkward as a slug.** Not a problem — the slug is always `me`, not the user's name. The name goes in the frontmatter `title:` and in the body heading.
- **User is running Meditations for a team or a shared persona.** Onboarding still produces one `me.md`. The self page is the curator's perspective, not a multi-user table. If the user wants a team-shared wiki, that is a customization they can make after onboarding by editing the file.

## Self-observation triggers

Append to CHANGELOG.md if:
- The user consistently skips question 4 (tone preference might not be load-bearing — consider dropping it).
- The user consistently gives multi-paragraph answers that feel like brief-shaped content (maybe onboarding should offer to file a first brief at the end).
- A user re-onboards repeatedly, indicating the self page is not capturing what they actually want (the question set may need revisiting).
- The user asks to add a field that is not in the template (log it; if the same field is requested 3+ times, add it).
