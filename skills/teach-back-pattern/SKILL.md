---
name: marcus-teach-back-pattern
description: "Stress-test a pattern using the user's own explanation, capture their framing verbatim"
tags: [wiki, teach-back, patterns, verification]
user-invocable: true
metadata:
  openclaw:
    emoji: "🪞"
---

# Teach-Back Pattern Skill

## When to trigger

- Immediately after wiki-compile proposes a new pattern (optional; the user can defer)
- Manual: "teach me back the pattern about X", "stress-test [slug]", "teach-back X"
- Before relying on a pattern for a high-stakes situation (interview, difficult conversation). The user may ask explicitly.

Do NOT auto-run after every compile. Only offer it when a pattern feels new or fragile.

## What to read

| File | Why |
|---|---|
| The target concept article | The pattern being stress-tested |
| Source observations for that article | Ground truth the pattern was built from |
| Related entity pages | Context for exceptions (e.g., who does the pattern NOT work with) |

## What to write

| File | When |
|---|---|
| The target concept article | Add the user's verbatim framing in a tagged section, add any new edges they surface |
| `wiki/log.md` | Append a `teach-back` entry |

## Behavior

### Step 1 — Present the pattern

Show the user:
- The pattern name and summary
- The key claim in 2-3 sentences
- The source observations that support it (list with dates)
- Any existing `## Edges and exceptions` already recorded

Keep it compact. This is the "here is what I think I know about this" moment.

### Step 2 — Ask the user to explain it back

Prompt:
> "Explain this back to me in your own words. How would you tell someone else what the pattern is and when to use it?"

Wait for the response. Do not interrupt, do not lead.

### Step 3 — Compare their framing to yours

Note:
- **Agreements:** places where their words match the article. Good signal, the pattern holds.
- **Gaps:** things in the article they did not mention. Maybe they are not load-bearing.
- **Disagreements:** places where their words contradict the article. This is the most important signal — the article may be wrong.
- **Additions:** things they said that are NOT in the article yet. These are often the most valuable, because they reveal tacit knowledge the observations did not capture.

### Step 4 — Probe for edges

Ask:
> "Where does this NOT hold? What are the exceptions? Is there a person or situation where you would not use this?"

Capture every exception they name, even if it seems minor.

### Step 5 — Update the article

Add a section (create if missing):

```markdown
## In the user's words
<!-- user-framing: true -->

_Teach-back [YYYY-MM-DD]_

[The user's verbatim explanation of the pattern, in their phrasing]
```

This section is protected. Future compiles must not rewrite it.

If the user surfaced new edges, extend `## Edges and exceptions`:
```markdown
## Edges and exceptions

- **[YYYY-MM-DD, from teach-back]** [edge case in the user's words or a close paraphrase]
```

If the user disagreed with something in the article, do NOT silently fix it. Flag it in a `## Contradictions` section and note: "The user's teach-back on [date] disagreed with the earlier claim that [X]. Needs resolution."

### Step 6 — Update frontmatter and log

- Update the article's `updated` date.
- Append to `wiki/log.md`:
  ```
  ## [YYYY-MM-DD] teach-back | [article-slug] | [one-line: confirmed / revised / contradicted]
  ```

### Step 7 — Close out

One-line acknowledgement:
> "Captured your framing under `In the user's words` in [[slug]]. [N new edges added.] [Any contradictions flagged.]"

No summary, no recap of the whole article. They just explained it, they do not need to hear it back.

## Edge cases

- **User cannot explain the pattern:** this is important signal. The pattern may be premature — there is not enough real understanding yet. Do NOT write a framing section. Instead log: `teach-back | [slug] | deferred — insufficient grounding`. Suggest adding more observations before re-trying.
- **User's framing contradicts the entire article:** the article may be wrong. Flag prominently in `## Contradictions`, do not delete the article, and ask whether to archive or rewrite. The user decides.
- **User gives a very short answer:** capture it verbatim. Short is fine if it is their real thinking. Do not probe for more length for its own sake.
- **Pattern has no observations yet (stub article):** skip teach-back. Teach-back requires ground truth to stress against.

## Self-observation triggers

Log to CHANGELOG.md if:
- Teach-back revealed the article was substantially wrong (pattern detection is drifting)
- The user's framing is dramatically more concise than the article (Marcus may be over-elaborating)
- Teach-back repeatedly surfaces the same edge case (compile is missing it consistently)
