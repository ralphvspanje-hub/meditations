# Routines

Working examples of [Claude Code routines](https://claude.com/blog/introducing-routines-in-claude-code) that operate on the Meditations repo.

The pattern: keep the routine prompt in the claude.ai UI small (a bootstrap that locates the repo and points at an instructions file), and keep the actual routine logic versioned in the repo as a markdown file with an embedded bash script. Versioned instructions, minimal UI config. When you want to change behavior, you edit the markdown and commit; the routine picks up the new instructions on its next run.

Reusable template: [bootstrap.md](bootstrap.md) — the bootstrap prompt template all routines share, with placeholders for schedule, routine filename, repo name, and read-only-vs-write.

First concrete example: [random-observation.md](random-observation.md) — a daily push that picks one old observation and DMs it via Telegram. Read-only on the repo, send-only on the messenger.

For the conceptual walkthrough (bootstrap-plus-file split, the double-nested path gotcha, what a claude.ai environment is, read-only versus write routines), see [Writing a routine — the pattern](../docs/phone-setup.md#writing-a-routine--the-pattern) in phone-setup.md.

Configure routines at [claude.ai/code/routines](https://claude.ai/code/routines).

What routines are not for: anything that needs back-and-forth. A routine cannot ask a follow-up. See [phone-setup.md](../docs/phone-setup.md#scheduled-pushes-claude-routines) for the full not-fit list.
