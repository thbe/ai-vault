# Activity Log

Append-only chronological record of operations on this vault. Entry format defined in [[AGENTS#6. log.md format]].

Quick recall: `grep "^## \[" log.md | tail -20`.

---

## [2026-05-13] meta | Vault bootstrapped with AGENTS.md and llm-wiki registration

- Authored [[AGENTS]] as the operating contract — adapted from sibling `sap-vault` to fit ai-vault's curated MOC structure (TitleCase folders, no raw/wiki split).
- Registered vault as `ai-vault` in the `llm-wiki` skill registry; updated `SKILL.md` `description:` to include AI-coding trigger keywords.
- Created this `log.md` per §6 of [[AGENTS]].
- Notes created: [[AGENTS]], `log.md`
- Notes updated: —
- Follow-ups:
  - Backfill `last_verified` and `status` frontmatter on existing notes during next lint pass.
  - Decide whether to populate `raw/` (currently optional in §2) for upcoming ingests.

## [2026-05-13] ingest | YouTube — "The Limits of AI" (rBlCOLfMYfw)

- Source: IBM-style explainer (narrator Jeff). Auto-caption transcript pulled via `yt-dlp`, saved to `raw/transcripts/limits-of-ai-rblcolfmyfw.txt`. First content under `raw/`; folder created on this ingest.
- Scope decision: most of the video (AGI, consciousness, sustainability, sensation, deep emotions, AI history) is **out of scope** per [[AGENTS#1. Purpose]]. No standalone note created — would dilute the vault. Only the parts touching agentic-coding practice were lifted into existing notes.
- Notes created: —
- Notes updated:
  - [[Agentic Coding Paradigm]] — added "Macro vs. micro goals" callout (humans own *what*/*why*, agents own *how*).
  - [[Common Failure Patterns]] — in §2 (Confident-but-broken), added callout naming RAG / MoE / model chaining as upstream hallucination mitigations.
  - [[Context Engineering]] — added optional DIKW pyramid framing callout near the top.
  - [[Resources]] — new entry `[^limits-of-ai-yt]` under new "Talks & explainers" section, with link to local transcript copy.
- Follow-ups:
  - ✅ Done (next ingest): `raw/transcripts/README.md` written documenting fidelity caveat, naming convention, yt-dlp command.
  - ⏭ Skipped per user: "don't bet against AI" callout in [[About]].

## [2026-05-13] ingest | YouTube — "Die simple Aufgabe, an der jede KI scheitert" (x_ZhqjbgdAQ)

- Source: German-language YouTube explainer. Auto-caption transcript (German only — English fetch was 429-rate-limited) saved to `raw/transcripts/simple-aufgabe-an-der-jede-ki-scheitert-x_zhqjbgdaq.txt`.
- Scope decision: video centres on the **Lucas-Penrose / Gödel argument** (which the video itself flags as "kontrovers diskutiert"). Initial proposal was to skip it as out-of-scope philosophy. User overrode: practical and theoretical limits *are* in scope precisely because they justify why prompt/context engineering, plan-build-verify, and human-in-the-loop matter — *"like knowing the limits of your tires in the corners to maximise output."* Penrose included as **explanatory framing**, not endorsement; flagged with a `> [!warning] Contested` callout.
- Notes created:
  - [[Practical Limits of AI]] — new note covering statelessness, distribution-bound reasoning, the contested theoretical ceiling, and the implications-for-practice mapping to the rest of the vault. Racetrack metaphor as TL;DR framing.
- Notes updated:
  - [[Agentic Coding Paradigm]] — extended "Macro vs. micro goals" callout with the AI-as-tool / expertise-more-decisive reinforcement and `[^simple-aufgabe-yt]`. Added [[Practical Limits of AI]] to See also.
  - [[Context Engineering]] — added "Why this discipline exists at all" tip callout (statelessness as the architectural reason). Linked [[Practical Limits of AI]] in See also.
  - [[Common Failure Patterns]] — inserted new pattern **#12 Distribution-bound surprise** (renumbered old #12 → #13). Linked [[Practical Limits of AI]] in See also.
  - [[Resources]] — added `[^simple-aufgabe-yt]` row in Talks & explainers + footnote definition. Caveats noted (German, contested).
  - [[_Index]] — added [[Practical Limits of AI]] under 🧠 Modern AI Coding; bumped folder count 5→6.
- Follow-ups:
  - English transcript still missing (only German captured). Re-fetch with cooled rate-limit when convenient and add side-by-side under same `[^simple-aufgabe-yt]` entry.
  - The "industrial revolution / cognitive routine work displacement" thread is touched in §5 of [[Practical Limits of AI]] but not developed elsewhere — could become its own short note if a third source corroborates.

## [2026-05-13] ingest | Karpathy gist — "LLM Wiki"

- Source: Andrej Karpathy gist describing the LLM-maintained persistent wiki pattern (vs. one-shot RAG). Pulled via `webfetch`; not stored locally (gist URL is canonical and stable).
- Significance: this vault is a direct instance of the pattern. Ingest/query/lint operations, `index.md` + `log.md` with grep-able prefix, three-layer architecture (raw / wiki / schema) — all already present in our [[AGENTS]]. Either Karpathy's gist inspired our setup or convergent design; either way the lineage is now made explicit.
- Notes created:
  - [[LLM Wiki Pattern]] — full write-up of the pattern with explicit "this vault as an instance" mapping table and a "deviations worth knowing" section (TitleCase filenames, `_Index.md` leading underscore, four-bucket split, single Resources file).
- Notes updated:
  - [[Context Engineering]] — extended statelessness callout with a paragraph linking the per-session discipline to its vault-scale formalisation in [[LLM Wiki Pattern]].
  - [[Agentic Coding Paradigm]] — added [[LLM Wiki Pattern]] to See also (humans-curate / LLM-bookkeeps applied to knowledge work).
  - [[Resources]] — renamed "Talks & explainers" → "Talks & essays"; added `[^llm-wiki-gist]` row + footnote.
  - [[_Index]] — added [[LLM Wiki Pattern]] under 🧠 Modern AI Coding; bumped folder count 6→7.
- Decisions:
  - Title `LLM Wiki Pattern` chosen over `LLM Wiki` to avoid conflation with the `llm-wiki` skill in dotfiles.
  - [[About]] left untouched per prior user instruction, despite being a natural place to mention the pattern. Recommend revisiting next time About is touched.
  - [[AGENTS]] left ops-focused (no §1 amendment); the new note carries the conceptual lineage.
- Follow-ups:
  - When [[About]] is next edited, consider adding a one-sentence "this vault is an instance of the [[LLM Wiki Pattern]]" callout.
  - The gist mentions `qmd` (hybrid-search MCP); if the vault grows past ~100 sources, evaluate adopting it. Currently no need — the [[_Index]] suffices.

## [2026-05-13] ingest | GSD (Get Shit Done) README

- Source: <https://github.com/gsd-build/get-shit-done> (README, fetched via `webfetch`). Multi-runtime spec-driven workflow by TÂCHES, MIT, `npx get-shit-done-cc`. User intent flagged as "I also use GSD" — included as an active part of the practice, written neutrally pending confirmation of "we use it" framing.
- Significance: GSD is best read as an opinionated *bundle* of techniques this vault already documents — not a new paradigm. Direct mappings: fresh sub-agent contexts → [[Sub-Agents and Delegation]]; persistent `STATE.md`/`CONTEXT.md`/`ROADMAP.md` artifacts → [[LLM Wiki Pattern]] applied to project state; `/gsd-discuss-phase` → `/gsd-plan-phase` → `/gsd-execute-phase` → `/gsd-verify-work` → [[Plan-Build-Verify Workflow]]; "context rot" as the named failure mode → [[Context Engineering]]. Two notable design choices: (1) explicit `discuss` step before plan to capture user-imagined implementation decisions; (2) explicitly built around `--dangerously-skip-permissions` for unattended automation.
- Notes created:
  - [[GSD (Get Shit Done)]] in `Ecosystem/` — TL;DR + six-command loop table + three-problems-it-solves section (each cross-linking to the relevant Modern AI Coding note) + persistent artifacts list with explicit LLM-Wiki-Pattern callout + configuration brief + runtime/permissions stance + "where it sits relative to this vault" mapping table + quotables.
- Notes updated:
  - [[Context Engineering]] — added `> [!info] "Context rot" — the named failure` callout in anti-patterns section; added `[^gsd-readme]` to Sources.
  - [[Sub-Agents and Delegation]] — added `> [!example] Real-world implementation` callout pointing at GSD's parallel-wave executor architecture; added `[^gsd-readme]` to Sources.
  - [[Plan-Build-Verify Workflow]] — added "Concrete instantiations" mapping table (Anthropic best practices / OpenCode plan-build modes / GSD six commands); added `[^gsd-readme]` to Sources.
  - [[LLM Wiki Pattern]] — added cross-link in See also: same persistent-artifact insight applied to project state.
  - [[Permissions]] — added "When projects opt out entirely" section flagging GSD as the canonical example of `--dangerously-skip-permissions` by design; added `[^gsd-readme]` to Sources.
  - [[Resources]] — added `[^gsd-readme]` row + footnote.
  - [[_Index]] — added [[GSD (Get Shit Done)]] under 🌐 Ecosystem; bumped Ecosystem folder count 2→3.
- Decisions:
  - Folder: `Ecosystem/` (concrete cross-runtime tool, sits with [[Model Context Protocol]] and [[AGENTS.md Standard]]). Methodology aspects link *into* `Modern AI Coding/` notes rather than duplicating them.
  - Title: `GSD (Get Shit Done)` with aliases `[GSD, Get Shit Done]` for wikilink resolution either way.
  - Coverage: concise overview + cross-links, not exhaustive command/flag documentation. Upstream `docs/` change faster than we can track.
  - The README's `$GSD` Solana token badge ignored — irrelevant to the engineering value.
- Follow-ups:
  - User confirmed active GSD usage. Note now carries a "Used here" callout marking it as part of the active local toolkit. Open follow-up: capture local GSD config conventions and lessons-learned as they accrete from real-project use.
  - If/when adopting GSD's `discuss` step in this vault's own workflow, consider mirroring it explicitly in [[AGENTS]] §4.1 (currently we already discuss-before-write at step 2; could rename for vocabulary alignment).
