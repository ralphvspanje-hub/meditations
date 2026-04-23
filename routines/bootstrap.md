# Bootstrap prompt template for routines

Bootstrap prompt template for Claude Code routines that operate on this repo. Paste the block below into a new routine at [claude.ai/code/routines](https://claude.ai/code/routines), substitute the placeholders with real values, and you are done.

## Placeholders

- `<schedule>` — the time zone and time you want the routine to fire (e.g. `08:00 Europe/Amsterdam`).
- `<routine-filename>` — the name of the routine file in `routines/` that the routine should follow (e.g. `random-observation`, without the `.md` extension). The file must exist in your repo at `routines/<routine-filename>.md`.
- `<repo-name>` — the name of your GitHub repo exactly as it is cloned (e.g. `meditations`, `my-wiki`, whatever you named your fork). This controls the probe-loop paths below. If you rename the repo, update this in every routine's bootstrap.
- `<read-only-or-write>` — either `READ-ONLY — never edit, commit, or push` for read-only routines (like the daily push) or `may write — routine commits and pushes at the end` for routines that modify the repo.

## Template

```
Running in the cloud at <schedule>. Follow routines/<routine-filename>.md in the <repo-name> repo exactly and run to completion.

=== FIRST: find the repo root ===

Claude Code on the web clones each repo into a directory of the same name. The cloud clone path varies (sometimes /home/user/<repo-name>/, sometimes /home/user/<repo-name>/<repo-name>/). Locate it dynamically:

for p in /home/user/<repo-name>/<repo-name> /home/user/<repo-name>; do
  if [ -f "$p/routines/<routine-filename>.md" ]; then cd "$p"; break; fi
done
ls routines/<routine-filename>.md   # must succeed

If both fail, log a message and exit 1 — do not retry, do not commit anything.

=== Cloud-specific notes ===

- The repo is <read-only-or-write>.
- No human is watching. Run autonomously. Never prompt for approval, never wait for input.
- Exit 0 on success, exit 1 on failure (after logging the failure).
```

## Example — daily push (read-only)

Filled in for the `random-observation` routine with `<repo-name>` = `meditations` and `<schedule>` = `08:00 Europe/Amsterdam`:

```
Running in the cloud at 08:00 Europe/Amsterdam. Follow routines/random-observation.md in the meditations repo exactly and run to completion.

=== FIRST: find the repo root ===

Claude Code on the web clones each repo into a directory of the same name. The cloud clone path varies (sometimes /home/user/meditations/, sometimes /home/user/meditations/meditations/). Locate it dynamically:

for p in /home/user/meditations/meditations /home/user/meditations; do
  if [ -f "$p/routines/random-observation.md" ]; then cd "$p"; break; fi
done
ls routines/random-observation.md   # must succeed

If both fail, log a message and exit 1 — do not retry, do not commit anything.

=== Cloud-specific notes ===

- The repo is READ-ONLY — never edit, commit, or push.
- No human is watching. Run autonomously. Never prompt for approval, never wait for input.
- Exit 0 on success, exit 1 on failure (after logging the failure).
```
