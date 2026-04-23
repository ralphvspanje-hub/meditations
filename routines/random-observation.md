# random-observation — daily push of one old observation

## What this routine does

Runs daily at a scheduled time, picks one observation at random from `wiki/observations/`, formats it with a relative age phrase and a small decorative prefix, and DMs it via Telegram. Read-only on the repo. No writes, no commits, no push. If the push fails, the routine logs and exits non-zero; it does not retry and does not escalate to github.

The bootstrap prompt that goes into the claude.ai routine UI is generated from the template in [bootstrap.md](bootstrap.md). For this routine the filled-in example at the bottom of that file is what you paste — adjust only `<repo-name>` if you forked and renamed, and `<schedule>` if you want a different time.

## Routine config

Fill these into the claude.ai UI:

- **Name:** `random-observation`
- **Schedule:** daily at `08:00 Europe/Amsterdam` (example)
- **Repo:** the GitHub repo where this file lives
- **Environment:** a claude.ai environment with a Telegram connector and network access (see [Writing a routine — the pattern](../docs/phone-setup.md#writing-a-routine--the-pattern) in phone-setup.md for what an environment is)
- **Env vars required in the environment:** `TELEGRAM_BOT_TOKEN`, `TELEGRAM_CHAT_ID`

## Instructions

The routine follows these instructions on each run.

```bash
set -u

OBS_DIR="wiki/observations"

if [ ! -d "$OBS_DIR" ] || [ -z "$(ls -A "$OBS_DIR"/*.md 2>/dev/null)" ]; then
  curl -s -w "\n%{http_code}" -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
    --data-urlencode "chat_id=${TELEGRAM_CHAT_ID}" \
    --data-urlencode "text=No observations in the wiki yet."
  exit 0
fi

SELECTED=""
for attempt in 1 2 3 4 5; do
  CANDIDATE=$(ls "$OBS_DIR"/*.md | shuf -n 1)
  BODY=$(awk 'BEGIN{fm=0} /^---$/{fm++; next} fm>=2{print}' "$CANDIDATE")
  CHARS=$(printf '%s' "$BODY" | tr -d '[:space:]' | wc -c)
  if [ "$CHARS" -ge 30 ]; then
    SELECTED="$CANDIDATE"
    break
  fi
done

if [ -z "$SELECTED" ]; then
  curl -s -w "\n%{http_code}" -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
    --data-urlencode "chat_id=${TELEGRAM_CHAT_ID}" \
    --data-urlencode "text=No suitable observation found after 5 retries."
  exit 1
fi

FILENAME=$(basename "$SELECTED")
OBS_DATE=$(echo "$FILENAME" | grep -oE '^[0-9]{4}-[0-9]{2}-[0-9]{2}')
OBS_EPOCH=$(date -d "$OBS_DATE" +%s)
TODAY_EPOCH=$(date +%s)
AGE_DAYS=$(( (TODAY_EPOCH - OBS_EPOCH) / 86400 ))

if [ "$AGE_DAYS" -eq 0 ]; then
  AGE_PHRASE="Today you wrote this"
elif [ "$AGE_DAYS" -eq 1 ]; then
  AGE_PHRASE="Yesterday you wrote this"
elif [ "$AGE_DAYS" -lt 14 ]; then
  AGE_PHRASE="${AGE_DAYS} days ago you wrote this"
elif [ "$AGE_DAYS" -lt 60 ]; then
  WEEKS=$(( AGE_DAYS / 7 ))
  AGE_PHRASE="${WEEKS} weeks ago you wrote this"
else
  MONTHS=$(( AGE_DAYS / 30 ))
  AGE_PHRASE="${MONTHS} months ago you wrote this"
fi

BODY=$(awk 'BEGIN{fm=0} /^---$/{fm++; next} fm>=2{print}' "$SELECTED")
CLEANED=$(printf '%s' "$BODY" \
  | sed 's/^# //' \
  | sed '/^_From \[\[briefs\/.*\]\]_$/d' \
  | sed -E 's/\[\[[^]|]*\|([^]]+)\]\]/\1/g' \
  | sed -E 's/\[\[[^]]*\/([^]]+)\]\]/\1/g' \
  | sed -E 's/\[\[([^]]+)\]\]/\1/g' \
  | sed -E 's/\*\*([^*]+)\*\*/\1/g' \
  | sed -E 's/\*([^*]+)\*/\1/g' \
  | sed -E 's/__([^_]+)__/\1/g' \
  | sed -E 's/_([^_]+)_/\1/g' \
  | awk 'BEGIN{blank=0} /^[[:space:]]*$/{blank++; if(blank<=1)print; next} {blank=0; print}')

ICON=$(printf '📜\n🏛️\n🦉\n🏺\n🪶\n⚖️\n🕊️\n☀️\n' | shuf -n 1)

MESSAGE="${ICON} ${AGE_PHRASE}:

${CLEANED}"

RESPONSE=$(curl -s -w "\n%{http_code}" -X POST "https://api.telegram.org/bot${TELEGRAM_BOT_TOKEN}/sendMessage" \
  --data-urlencode "chat_id=${TELEGRAM_CHAT_ID}" \
  --data-urlencode "text=${MESSAGE}")

HTTP_CODE=$(echo "$RESPONSE" | tail -n1)
BODY_RESP=$(echo "$RESPONSE" | sed '$d')

if [ "$HTTP_CODE" != "200" ]; then
  echo "Telegram send failed: HTTP $HTTP_CODE"
  echo "$BODY_RESP"
  exit 1
fi

echo "Sent: $FILENAME"
exit 0
```

## Guardrails

- Read-only on the Meditations repo. No edits, no commits, no push.
- No interactive prompts. The script runs start to finish.
- Exit 0 on success, exit 1 on failure. Failure is logged, not escalated.

## Design notes

- Scope: `wiki/observations/*.md` only. Concepts, briefs, and sources are excluded.
- Unit: the whole observation file. Title, body, and any `**Source:**` line all ship.
- Randomization: uniform, no state. For a wiki with 50 observations fired daily, expect roughly a 50-day cycle. Add dedup state only if repeats start feeling stale.

## Prerequisites

- `shuf`, `awk`, `curl`, `date` present in the cloud environment (the standard Linux cloud env provides these).
- `TELEGRAM_BOT_TOKEN` and `TELEGRAM_CHAT_ID` configured in the routine's environment.
