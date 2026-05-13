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
