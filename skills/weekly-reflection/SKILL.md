---
name: marcus-weekly-reflection
description: "Sunday prompt to surface observations the user forgot to save during the week"
tags: [wiki, reflection, weekly]
user-invocable: true
metadata:
  openclaw:
    emoji: "🌙"
---

# Weekly Reflection Skill

## When to trigger

- **Sundays**, when the user explicitly invokes it: "weekly reflection", "sunday reflection", "let's reflect", "reflect on the week"
- If the user asks "what should I do" or "anything to review" on a Sunday, Marcus may suggest it: "It is Sunday. Want to do a weekly reflection? It usually takes about 5 minutes."
- If 3 or more Sundays have passed since the last reflection, Marcus may mention it ONCE on the next invocation of any skill:
  > "It has been [N] weeks since the last reflection. Want to do one?"
  
  If the user declines, drop it and do not re-mention until the next multiple of 3 weeks.

Never auto-trigger. Never push notifications. Never nag.

## What to read

| File | Why |
|---|---|
| `wiki/log.md` | Last 7 days of activity |
| `wiki/observations/` | Observations saved in the last 7 days (to avoid re-asking about things already captured) |

## What to write

| File | When |
|---|---|
| `wiki/observations/*.md` | For each new observation surfaced — use observation-drop skill as the actual writer |
| `wiki/log.md` | Append a single `reflection` entry at the end summarizing how many new observations were captured |

## Behavior

### Step 1 — Check what is already captured

Read observations from the last 7 days. Know what the user already saved so you do not ask about it again.

### Step 2 — Ask 3 or 4 questions, one at a time

Ask them conversationally, not as a checklist. Wait for the answer before the next one.

Suggested questions (pick 3-4, adapt to context):

1. "Anything from this week at work worth capturing that you did not already save?"
2. "Any pattern you noticed that surprised you? Something that worked in a way you did not expect, or failed in a way you did not expect?"
3. "Any person you interacted with differently than usual? Any new signal about how someone operates?"
4. "Anything that worked or failed, beyond what you already logged?"
5. "Anything you were thinking about this week that you wanted to come back to?"

Adapt based on context. If last week had lots of observations about a specific topic or person, ask a follow-up about them. If the week was quiet, ask fewer questions.

### Step 3 — Capture each response as an observation

For each response that contains a real observation, hand off to the observation-drop skill — do not re-implement its logic. Write the observation file with the format from observation-drop's SKILL.md. Use today's date (Sunday).

Tag reflection-surfaced observations with `source: weekly-reflection` in the body of the observation file so they are identifiable later.

If the user gives a non-answer ("nothing comes to mind", "quiet week"), accept it. Do not push.

If the reflection produces a long multi-claim answer (a Sunday catch-up dump with several distinct things noticed), hand off to brief-drop instead of observation-drop. Tag the brief body with `source: weekly-reflection` so it is identifiable later. Weekly-reflection's question-by-question cadence is preserved; brief-drop is only the filing path for one oversized answer.

### Step 4 — Close out

One-line summary:
> "Captured [N] observations from this reflection. [If any are clustered with recent ones, mention it.] See you next Sunday."

Keep it short. No pep talk. No summary of the week's themes (that is what compile is for).

### Step 5 — Log the reflection

Append to `wiki/log.md`:
```
## [YYYY-MM-DD] reflection | [N new observations captured]
```

### Step 6 — Check compile threshold

If the reflection pushed unprocessed observations to 5+, mention it once:
> "You now have [N] unprocessed observations. Want me to compile them later this week?"

Do not auto-run compile. Reflections are for capture, not synthesis.

## Edge cases

- **User has nothing to say:** fine. Log a reflection entry with 0 observations and move on. Some weeks are quiet.
- **User launches into a long story:** let them finish. Capture it as one observation, not five. The shape of the narrative is signal.
- **User wants to reflect on a non-Sunday:** let them. Do not enforce the day. Log as reflection regardless.
- **User wants to do multiple reflections in a week:** also fine. The cadence is suggested, not enforced.
- **Marcus has not been invoked for a month:** do the "it has been N weeks" mention on first re-invocation, then drop it if declined.

## Self-observation triggers

Log to CHANGELOG.md if:
- Reflection produces zero observations 3 weeks in a row (maybe the questions need rephrasing, or the user is in a quiet period)
- A reflection surfaced an observation that turned out to be a load-bearing pattern (reflection is working)
- The user consistently adds observations about the same topic during reflections (that topic may deserve its own proactive prompt during the week, not just on Sunday)
