# Marcus Changelog

## 2026-04-23 — cleanup after SETUP.md, README Agents section

**What happened:** the SETUP.md landing pass missed three stale references and left the README's closing agent section scoped to Coach only. README line 89 still told readers to paste the setup docs after `Follow @docs/COACH.md`, which contradicted both the new `Setup walkthroughs` block six lines above and the updated pointers inside both setup docs. `docs/phone-setup.md` line 3 still teased "a coach session will walk you through the pieces" even though the pointer directly below it now routes to SETUP. `docs/obsidian-graph-colors.md` line 5 name-dropped "Farzapedia's Explicit principle," an orphan reference to a private file that does not live in the public repo. And the README's closing `## Coach` section described one agent when there are now three — a cold reader could not see the Marcus / Setup / Coach split without reading three separate files. This pass deletes the stale README paragraph (the `claude.ai/code/routines` URL it carried is already surfaced in `docs/phone-setup.md`, which the README links to), swaps "coach session" to "setup guide session" in the phone-setup tease, drops the Farzapedia clause (the surrounding "plain-text documentation any agent can read without Obsidian running" justification stands on its own), and renames `## Coach` to `## Agents` with one short paragraph per agent so the three-job split is visible from the README alone.

**Changed:** deleted stale COACH-pointer paragraph from `README.md`; renamed `## Coach` section to `## Agents` covering Marcus, Setup, and Coach; swapped "coach session" to "setup guide session" in `docs/phone-setup.md` intro; dropped Farzapedia clause from `docs/obsidian-graph-colors.md`.

**File:** `README.md`, `docs/phone-setup.md`, `docs/obsidian-graph-colors.md`, `CHANGELOG.md`.

## 2026-04-23 — SETUP.md onboarding agent, README paste-in prompts

**What happened:** the "How to use this doc" pointers at the top of `phone-setup.md` and `cursor-setup.md` landed a cold reader inside Coach, which is a planning-partner agent for changes to Meditations itself — long design conversations, execution prompts for fresh executor sessions, stress-testing against the principles. That is the wrong agent for a user who just cloned the repo and wants hand-holding through one specific setup path. Coach would happily oblige, but the tone and shape are pitched at planning, not onboarding, and the user does not know to ask for something different. This pass introduces `docs/SETUP.md` as a second agent file purpose-built for onboarding: it walks one user through one of four paths (Cursor / VS Code extension, daily-observation routine, Claude Channels, Obsidian graph colors), asks what the user already has in place before recommending steps, defines jargon on first use, confirms each step landed before moving on, and ends with a verification test appropriate to the path. The two existing setup docs' "How to use this doc" pointers now reference `SETUP.md` instead of `COACH.md`. The README surfaces four paste-in prompts in a new `Setup walkthroughs` subsection under Install so a cold reader lands in a guided conversation without having to open a doc to start. Coach stays unchanged; the planner/executor split is preserved. SETUP.md is the onboarding guide, Coach is the planning partner, Marcus is the wiki maintainer — three agents, three jobs.

**Changed:** created `docs/SETUP.md`; added `Setup walkthroughs` subsection with four paste-in prompts to `README.md`; repointed the "How to use this doc" pointers in `docs/phone-setup.md` and `docs/cursor-setup.md` from COACH to SETUP; updated the closing paragraph of `docs/phone-setup.md` to match.

**File:** `docs/SETUP.md`, `README.md`, `docs/phone-setup.md`, `docs/cursor-setup.md`, `CHANGELOG.md`.

## 2026-04-23 — two paths instead of three, decision block, always-on honesty

**What happened:** successive additions had let the phone docs grow an implicit third path — API-triggered routines — even though the setup friction (auth tokens, payload plumbing, Shortcut round-trip) never matched what the repo ships. A new reader arriving at `phone-setup.md` had to parse three paths where only two are supportable out of the box, and `cursor-setup.md` was quietly recommending API-triggered routines as a turnkey quick-capture option. This pass restructures `phone-setup.md` around exactly two paths (scheduled routines and Channels), adds a "Pick your path" decision table immediately after Primary sources so a reader picks in 30 seconds, reorders the body so the lower-commitment routines path is covered first and Channels second, tightens the Channels prerequisite to name what always-on really means (Mac Mini, home server, VPS, or a laptop that literally never closes — a system-update restart breaks the pairing), and demotes API-triggered routines from a full subsection to a one-paragraph acknowledgement at the end of "Writing a routine — the pattern" (the option exists, one author runs it, the repo does not ship a worked example because the wiring is finicky). The "The two combined" closer drops the "fully phone-operable" overclaim. README's folder map gains the two previously missing docs files (`phone-setup.md`, `cursor-setup.md`). `cursor-setup.md` stops implying a three-path repo and reframes the author's API-triggered routine as one bespoke addition rather than a supported third option.

**Changed:** restructured `docs/phone-setup.md` (two-path decision table, routines-first body order, API-triggered demoted to one paragraph, always-on wording tightened, closer rewritten); expanded README folder map; tightened two paragraphs in `docs/cursor-setup.md`.

**File:** `docs/phone-setup.md`, `README.md`, `docs/cursor-setup.md`, `CHANGELOG.md`.

## 2026-04-23 — reusable bootstrap template, prerequisites subsection, single source of truth

**What happened:** the routine-bootstrap text was duplicated between the claude.ai routine UI and `routines/random-observation.md`, creating two sources of truth that would drift. This pass promotes the bootstrap to a reusable template at `routines/bootstrap.md` with placeholders (`<schedule>`, `<routine-filename>`, `<repo-name>`, `<read-only-or-write>`) plus one filled-in example. `routines/random-observation.md` loses its duplicate prompt block and just points at the template. `docs/phone-setup.md` gains a new "Prerequisites for routines" subsection that spells out the three things a new reader needs in place before any routine works (their own GitHub repo, a claude.ai environment with the right connectors and env vars, repo-name awareness for the probe-loop paths) and its inline probe-loop snippet is replaced by a pointer to the template. Three changes together close the "works in principle, not in practice" gap that blocked plug-and-play.

**Changed:** created `routines/bootstrap.md`; slimmed `routines/random-observation.md`; updated `routines/README.md`; expanded `docs/phone-setup.md`.

**File:** `routines/bootstrap.md`, `routines/random-observation.md`, `routines/README.md`, `docs/phone-setup.md`, `CHANGELOG.md`.

## 2026-04-23 — concrete routine example and pattern subsection

**What happened:** phone-setup.md previously told readers routines were an option but left them to reverse-engineer the prompt shape, the cloud clone path gotcha, the environment setup, and the script mechanics. This pass ships a complete working example at `routines/random-observation.md` (daily push: pick a random observation, format it with a relative age phrase and a decorative icon, DM via Telegram; read-only on the repo, send-only on the messenger) and adds a "Writing a routine — the pattern" subsection to phone-setup.md covering the bootstrap-plus-file split, the double-nested-path gotcha, what a claude.ai environment is, and the read-only-versus-write distinction. One zoomed-out screenshot of the routine edit UI is embedded. The app URL at claude.ai/code/routines (which is not linked from the main nav) is surfaced in three places — phone-setup.md primary-sources, the pattern subsection, and the README — so readers do not have to hunt for it. README gains a one-line Coach-walkthrough pointer after the phone subsection.

**Changed:** created `routines/`, `routines/README.md`, `routines/random-observation.md`; expanded `docs/phone-setup.md`; added pointer in `README.md`.

**File:** `routines/README.md`, `routines/random-observation.md`, `docs/phone-setup.md`, `README.md`, `CHANGELOG.md`.

## 2026-04-23 — cursor-setup doc, API-triggered routines subsection

**What happened:** The repo now explicitly names three real capture paths (Cursor/VS Code extension, Claude Channels from phone, Claude routines for scheduled and API-triggered captures) instead of implicitly assuming terminal-only. The new `docs/cursor-setup.md` names the IDE path as the recommended default for users without a 24/7 machine, explains the fresh-window-per-capture discipline as the manual equivalent of the coordinator-subagent pattern, and closes with a concrete first-person account of how the author pairs Cursor as daily driver with API-triggered routines for phone quick-capture. The `phone-setup.md` routines section gains an API-triggered one-shot-save option so single-claim captures from a phone Shortcut are no longer wrongly in the not-fit bucket, and cross-references the Cursor doc's "How one author actually uses this" section for the working-setup example.

**Changed:** created `docs/cursor-setup.md`, updated `docs/phone-setup.md` with an API-triggered routines subsection, added `Capturing via Cursor or VS Code` subsection to `README.md`.

**File:** `docs/cursor-setup.md`, `docs/phone-setup.md`, `README.md`, `CHANGELOG.md`.

## 2026-04-23 — phone-setup doc, replace handwave in README

**What happened:** README's `Running it from your phone` paragraph claimed "any harness that can call Claude on a schedule works" and pointed at no specific infrastructure. That handwave let readers infer plug-and-play when phone access actually requires two distinct pieces of Anthropic infrastructure — Claude Channels for interactive capture into a running local session, and Claude routines for scheduled cloud-side pushes — each with different constraints (claude.ai login, Claude Code v2.1.80+, Bun, github sync as load-bearing for the routines path, the laptop staying on for the channels path). The new doc names both pieces honestly, links the primary sources, and ships a reference coordinator-subagent agent definition so a reader can copy the working shape without reverse-engineering it. The README now points at the doc instead of handwaving.

**Changed:** created `docs/phone-setup.md`, rewrote README's `Running it from your phone` subsection to point at the new doc.

**File:** `docs/phone-setup.md`, `README.md`, `CHANGELOG.md`.

## 2026-04-18 — brief-drop: testable-heuristic refactor (replaces the rule pile)

**What happened:** Iterating on brief-drop through the day accumulated several prescriptive rules (enumerated-list rule, atom integrity rule, continuation-arc merge rule, section-grouping pass, sibling-tag-parallelism check, Step 2b subagent) to stabilize atom-boundary decisions. Each new rule competed with earlier rules and got applied inconsistently across runs; the same failure shapes (enumerated sub-lists collapsed into a parent atom, trailing normative sentences absorbed, rationale clauses over-split) kept recurring. Atom-boundary decisions are semantic judgment; procedural rules have a ceiling for stabilizing that, and LLMs pattern-match against concrete examples more reliably than they follow long rule chains.

**Changed:** Major refactor of `skills/brief-drop/SKILL.md`. Replaced the Step 1 rule pile with a single functional criterion — *each atom is a testable heuristic* — operationalized as: (a) could the claim be evaluated true or false against a future situation, and (b) can it stand on its own without an adjacent claim. Three consequences stated inline (fold dependent clauses, split distinct heuristics, default to splitting enumerated items). Four worked micro-examples (A: enumerated sub-list; B: trailing normative sentence; C: rationale/caveat/qualifier clause; D: continuation across paragraphs) anchor each recurring shape. Section-grouping pass shortened to a two-paragraph note — sections drive tag parallelism only, not atom identification. Step 2's parallelism check simplified to one paragraph. Step 2b's review criteria refactored from five to four (Over-folded atoms, Over-split atoms, Sibling-tag parallelism, Entity drift), all anchored to the same testable-heuristic criterion; ATOMS TO SPLIT replaces ATOMS TO ADD in the subagent output format; SEGMENTATION FIXES removed. Self-observation triggers pruned to reference the examples rather than rules that no longer exist. Kept intact: Step 2b subagent pass, mandatory final-proposal block, Step 3 precondition, typo-fix pass, provenance detection, frontmatter conventions. Trades ~1000 words of prescriptive rule text for ~400 words of criterion plus examples.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-18 — brief-drop: approval-skip hole closed

**What happened:** Under the Step 2b silent-merge path (subagent delta under the 5-atoms-or-30%-tags size gate), Marcus could read "silent merge" as "proceed directly to Step 3" and file atoms, brief, log entry, and INDEX update without the user seeing the final proposal. The Step 2b text said the subagent runs between draft assembly and the proposal presentation, but the silent-merge branch never explicitly re-looped back to the Step 2 presentation step. The delta-size gate's user-prompt only surfaces the subagent's delta for large changes; it does not serve as the final proposal-approval checkpoint for small-delta paths.

**Changed:** Two belt-and-suspenders edits to `skills/brief-drop/SKILL.md`. First, Step 2b now ends with an explicit "Present final proposal to the user. This step is mandatory and never skipped" block stating that after any subagent merge (silent or size-gated), Marcus always presents the final proposal in the Step 2 format — full title, provenance, entities, every atom with title, verbatim quote, and tags — closing with "Proceeding to filing only on explicit approval." Second, Step 3 gains an opening precondition: Step 3 never runs without explicit approval from the user on the final post-subagent proposal. A silent subagent merge is not approval. A size-gate approval on the subagent's delta alone is not approval of the final proposal.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-18 — brief-drop: atom integrity rule (peer claims split, qualifiers merge)

**What happened:** Step 2b's first run over-split where the enumerated-list rule had under-split. Rationale, caveat, and qualifier clauses grammatically dependent on an adjacent claim were getting promoted to standalone atoms, weakening both the parent (which lost nuance or justification) and the orphan (which lost context). Step 1 and Step 2b both had clear guidance on WHEN to split and no guidance on when NOT to split.

**Changed:** Added the atom integrity rule to `skills/brief-drop/SKILL.md` Step 1 as the inverse of the enumerated-list rule: *peer claims split, qualifiers merge.* A non-enumerated clause is a distinct atom only if removing it does not weaken an adjacent atom's claim when the adjacent atom is read independently. Three linguistic-marker categories (rationale: "because / since / the reason is"; caveat: "unless / except when / but only if"; qualifier: "in particular / specifically / especially") are high-signal cues for merge. Enumerated-list items are exempt by construction. Step 2b's review criteria expand from four to five (new: "Over-splitting"). Subagent output format gains an ATOMS TO MERGE section peer to ATOMS TO ADD; merge behavior appends the over-split atom's quote back to the parent atom verbatim with no tag changes on the parent. Self-observation triggers gain an over-merging trigger (if the user rejects proposed merges, the dependency test is over-firing) and a majority-shared-sibling-tags note in the sibling-tag-gap trigger for a candidate future extension.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-18 — brief-drop: Step 2b reviewer subagent pass

**What happened:** The prescriptive Step 1 and Step 2 rules partially fixed tag drift and under-extraction on large briefs, but a structural blindspot remained: the same Marcus instance that segments sections and picks theme tags cannot reliably catch its own segmentation or theme-selection errors. Single-pass self-review misses judgment-call cases even when the mechanical rules fire correctly.

**Changed:** Added Step 2b to `skills/brief-drop/SKILL.md`, running between Step 2's draft assembly and the proposal presentation. An independent reviewer subagent receives three inputs (raw dump, Marcus's draft proposal, four review criteria) and returns a strictly-formatted delta with sections for missed atoms, tag additions, entities to drop, and segmentation fixes. Marcus merges the delta mechanically. A delta-size gate (5+ atoms or 30%+ tag changes) surfaces large rewrites before applying. Conflicts with the Step 1/2 prescriptive rules resolve in favor of the prescriptive rules. Self-containment is preserved: if no subagent dispatch is available in the running environment, the step is skipped and the prescriptive rules alone carry the load. Self-observation triggers rewritten to focus on double-misses (both layers missed), subagent under-firing, and over-firing.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-18 — brief-drop: enumerated-list rule, section-grouping, sibling-tag-parallelism

**What happened:** Large external-import briefs surfaced two failure modes: sibling atoms from the same source-section drifted on tag vocabulary (so compile fragmented what should cluster), and items inside enumerated lists got collapsed under surface-similarity judgments (so distinct claims got dropped).

**Changed:** `skills/brief-drop/SKILL.md` Step 1 gains an enumerated-list rule (every item in a numbered or bulleted list becomes a candidate atom, suppressed only on strict word-level paraphrase) and a section-grouping pass that builds a source-section → candidate-atoms map as a visible artifact. Step 2 gains a sibling-tag-parallelism check that runs before the proposal is assembled: for each section with 2+ siblings, derive the section's primary theme tag and silently ensure every sibling carries it. Two residual self-observation triggers were added for cases the prescriptive checks might miss.

**File:** `skills/brief-drop/SKILL.md`.

## 2026-04-16 — v1.0 initial public release

**What happened:** First public release of Meditations, an LLM-maintained personal learning wiki pattern. Based on Andrej Karpathy's "LLM Wiki" pattern and Farza's Farzapedia personalization principles.

**Changed:** Initial commit.

**File:** Everything.
