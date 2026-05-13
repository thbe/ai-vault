# AGENTS.md — Vault Schema & Operating Manual

> This file is the operating contract between the human and the LLM agent maintaining this vault.
> The human curates direction and asks questions. The LLM owns the note content and bookkeeping.
> Read this file at the start of every session.

---

## 1. Purpose

A curated knowledge base on **AI-assisted software engineering** with two intertwined focuses:

1. **OpenCode** — the terminal-first open-source AI coding agent by [Anomaly](https://anoma.ly).
2. **Modern AI coding practice** — patterns, workflows, and mental models for agentic development (context engineering, plan-build-verify, sub-agent delegation, MCP, AGENTS.md standard, etc.).

Goals:

- Maintain a **Map of Content (MOC)** that links every note via wikilinks; nothing is an island.
- Track durable concepts that survive model/tool churn — not weekly benchmark noise.
- Cite every non-trivial claim back to a primary source via [[Resources]].
- Stay current with upstream OpenCode docs and the broader agentic-coding ecosystem.

---

## 2. Directory layout

```
_Index.md             # MOC — top-level entry point (LLM-maintained)
About.md              # What the vault is, audience, conventions
Resources.md          # Single citations registry (LLM-maintained)
AGENTS.md             # This file
log.md                # Append-only chronological activity log (LLM-maintained)
README.md             # Brief orientation for humans

OpenCode/             # The OpenCode product itself
  OpenCode Overview.md
  Installation.md
  Configuration.md
  Agents.md
  Skills.md
  Rules and AGENTS.md.md
  MCP Servers.md
  Permissions.md

Modern AI Coding/     # Methodology, paradigms, patterns
  Agentic Coding Paradigm.md
  Context Engineering.md
  Plan-Build-Verify Workflow.md
  Sub-Agents and Delegation.md
  Common Failure Patterns.md

Ecosystem/            # Cross-tool standards & adjacent technologies
  Model Context Protocol.md
  AGENTS.md Standard.md

assets/               # Images, diagrams, screenshots referenced by notes

raw/                  # OPTIONAL — IMMUTABLE sources. LLM reads but never modifies.
  articles/           # Web clips, blog posts
  docs/               # Official doc excerpts, release notes
  pdfs/               # Books, whitepapers
  transcripts/        # Talk/podcast transcripts
```

**Rules:**

- LLM **never** writes to `raw/` or `assets/` (except adding new generated assets when explicitly asked).
- LLM **fully owns** all notes plus `_Index.md`, `Resources.md`, `log.md`. Edits freely.
- Folder names are **TitleCase with spaces** (e.g. `Modern AI Coding/`). Don't rename without proposing first — wikilinks across the vault depend on stable paths.
- Don't introduce new top-level folders without proposing the change. New notes go into one of the existing four buckets (`OpenCode`, `Modern AI Coding`, `Ecosystem`, root for meta).

---

## 3. Page conventions

### 3.1 Frontmatter

Every note carries frontmatter. Minimum:

```yaml
---
title: "Note Title"
tags: [opencode, pattern, concept, meta, ...]
---
```

Optional fields when applicable:

```yaml
aliases: ["Short Name", "Alt Spelling"]   # for [[link]] resolution
last_verified: 2026-05-13                 # date the note was checked vs. upstream
status: stub | draft | stable | deprecated
sources: ["[[Resources#some-anchor]]", ...]
related: ["[[Other Note]]", ...]
```

The MOC (`_Index.md`) carries `tags: [moc, index]` and `aliases: [Home, MOC, Index]`.

### 3.2 Filename convention

- **TitleCase with spaces.** E.g. `Context Engineering.md`, `MCP Servers.md`.
- Match the H1 inside the note. The wikilink `[[Context Engineering]]` should resolve cleanly.
- Acronyms keep their casing: `MCP`, `AGENTS.md`, `UI`.

This differs intentionally from sibling vaults (`sap-vault`, `azure-vault`) which use kebab-case. The ai-vault is a curated MOC, not an ingest pipeline — readability of the file explorer matters more than URL-safety.

### 3.3 Wikilinks

- Always use Obsidian wikilinks: `[[Context Engineering]]`, `[[OpenCode/Skills|Skills]]`.
- Prefer the bare title (`[[Context Engineering]]`) over the path form when the title is unique. Use the path form only to disambiguate.
- Use the alias `|` for readable inline text: `[[Plan-Build-Verify Workflow|the four-phase loop]]`.
- Section links: `[[Context Engineering#Anti-patterns]]`.

### 3.4 Standard sections

Notes are short and self-contained. Typical shape:

```markdown
---
title: "..."
tags: [...]
---

# {Title}

> [!abstract] TL;DR
> One-paragraph summary.

## What it is
## Why it matters
## How it works (or: Mechanics / Lifecycle)
## Examples
## Anti-patterns / Pitfalls
## Related
- [[...]]

## Sources
[^1]: See [[Resources#...]]
```

Adapt freely — these are starting points, not handcuffs.

### 3.5 Callouts & diagrams

- Use Obsidian callouts liberally: `> [!abstract]`, `> [!info]`, `> [!warning]`, `> [!tip]`, `> [!example]`.
- Use Mermaid for flowcharts/architecture (`graph LR`, `sequenceDiagram`).
- Tag code blocks with the language: ` ```bash `, ` ```json `, ` ```yaml `, ` ```typescript `.

### 3.6 Sources & citations

- All external sources are catalogued **once** in [[Resources]].
- Inline citations use markdown footnotes resolved in [[Resources]]:

```markdown
OpenCode supports custom sub-agents[^1].

[^1]: See [[Resources#opencode-docs-agents]].
```

- Avoid bare URLs in note body. If a link is authoritative enough to cite, it belongs in [[Resources]].

---

## 4. Operations

### 4.1 INGEST — adding a new source or topic

When the human points to a source (URL, file in `raw/`, pasted content) and says "ingest this":

1. **Read** the source fully. For PDFs use the `liteparse` skill; for diagrams/screenshots use `analysis-vision`; for live URLs use `webfetch`.
2. **Discuss** key takeaways in 3-6 bullet points before writing anything. Confirm direction with the human.
3. **Identify** which of the four buckets it belongs in (`OpenCode/`, `Modern AI Coding/`, `Ecosystem/`, or root meta) — or whether it spans several.
4. **Update existing notes** first. Add new info, refine claims, flag contradictions inline:
   > [!warning] Conflicting source
   > [^old] claims X; [^new] claims Y. Reason: ...
5. **Create new notes** only if the topic doesn't fit an existing one. Keep them small and focused.
6. **Add citations** to [[Resources]] (one canonical entry per source, anchored).
7. **Update** [[_Index]] if a new note was created or a section now warrants regrouping.
8. **Append** an entry to `log.md` (see §6).
9. **Report** to the human: notes touched, citations added, gaps spotted, suggested follow-ups.

### 4.2 QUERY — answering a question

1. Read [[_Index]] first to find candidate notes.
2. Read the relevant notes (and follow wikilinks one hop).
3. Synthesize an answer with **inline wikilink citations** to the notes used.
4. If the answer reveals a gap (missing note, stale claim, broken link) — note it and offer to fix.
5. **Offer to file the answer** as a new note if it's substantive and durable. Don't auto-file — ask first.
6. Append a short entry to `log.md`.

### 4.3 LINT — periodic health check

When the human says "lint the vault":

1. **Orphans**: notes with no inbound wikilinks (besides [[_Index]]).
2. **Broken wikilinks**: links that no longer resolve (renames, typos).
3. **Stubs**: `status: stub` notes older than N days that should be expanded.
4. **Stale claims**: notes with old `last_verified` dates pointing to fast-moving topics (model capabilities, OpenCode CLI flags).
5. **Missing cross-refs**: if note A mentions concept B by name but doesn't link it, suggest the link.
6. **Index drift**: notes that exist but aren't in [[_Index]], or vice versa.
7. **Citation hygiene**: claims without footnotes; entries in [[Resources]] no longer cited.
8. **Folder fit**: notes that drifted into the wrong bucket as their scope evolved.

Output a structured report. **Don't auto-fix** — propose changes and let the human prioritize.

### 4.4 REFACTOR — restructuring

When notes grow long, split them. When a folder gets crowded, propose subdividing **before** doing it (the four-bucket layout is intentional). Always grep for wikilinks before renaming and update every reference.

---

## 5. _Index.md format

The MOC. Grouped by bucket, with emoji section headers and short one-liners. Each note appears with a brief description.

```markdown
---
title: AI Documentation — Index
tags: [moc, index]
aliases: [Home, MOC, Index]
---

# AI Documentation Vault

> [!abstract] Purpose
> One-paragraph statement of what this vault covers.

This is a **Map of Content (MOC)**. Start here, then follow the wikilinks.

## 🧭 Start here
- [[About]] — what this vault is
- [[Resources]] — every external source

## 🛠 OpenCode
- [[OpenCode Overview]] — what it is, why it exists
- ...

## 🧠 Modern AI Coding
- ...

## 🌐 Ecosystem
- ...

## 🗺 At a glance
| Topic | Folder | Notes |
|---|---|---|
| OpenCode product | `OpenCode/` | N |
...

> [!info] Vault conventions
> - Last verified against upstream docs: **YYYY-MM-DD**.
```

Keep it tight and scannable. The MOC is the front door.

---

## 6. log.md format

Append-only. Every entry follows the same prefix so it's grep-able:

```
## [YYYY-MM-DD] {kind} | {short title}

- Summary line.
- Notes created: [[...]], [[...]]
- Notes updated: [[...]], [[...]]
- Notes / follow-ups.
```

`{kind}` ∈ `ingest | query | lint | refactor | meta`.

Quick recall: `grep "^## \[" log.md | tail -20`.

If `log.md` doesn't yet exist, create it on the first operation that warrants logging.

---

## 7. Workflow defaults (current)

- **Per-source supervision**: discuss takeaways before writing. Don't batch-ingest unless asked.
- **Durable over current**: prefer concepts that will still be true in 12 months. Capability claims about specific models/versions go in callouts with dates, not in body prose.
- **Cite, don't paraphrase**: if a claim is non-obvious, it gets a footnote into [[Resources]].
- **Small notes**: prefer 5-15 short cross-linked notes over one 800-line monster.
- **Wikilink density matters**: a note with no outbound wikilinks is suspicious. Connect it.

These are evolving — update this section as workflow preferences emerge.

---

## 8. Skills to leverage

- `obsidian-markdown` — wikilinks, callouts, frontmatter syntax.
- `obsidian-cli` — vault operations if/when needed.
- `obsidian-bases` — dynamic views over the vault via `.base` files.
- `liteparse` — parsing PDFs/unstructured docs in `raw/` (when that folder is populated).
- `analysis-vision` — diagrams, screenshots, architecture pictures.
- `webfetch` (built-in) — pulling upstream docs, blog posts, RFCs for ingest.

Load these on-demand via the `skill` tool, not preemptively.

### 8.1 External consumption: the `llm-wiki` skill

This vault is a candidate for registration with the `llm-wiki` skill (in `~/.config/opencode/skills/llm-wiki/registry.md`). When/if registered as `ai-vault`, the workflow for an external agent invoking that skill on an AI-coding topic will be:

1. Skill resolves the vault by trying registered candidate paths in order.
2. Skill reads **this `AGENTS.md`** as the authoritative operating contract.
3. Skill reads [[_Index]] (see §5) to discover notes.
4. Skill reads relevant notes, follows wikilinks one hop, respects `status` and `last_verified` from frontmatter (see §3.1).
5. Skill synthesizes an answer with wikilink citations, flags gaps — but **never edits this vault from outside**. Edits happen inside the vault per §4.

Sections that are **load-bearing for external agents**:

- §2 (directory layout) — so the skill knows where to look.
- §3 (page conventions, especially §3.1 frontmatter) — so the skill can interpret status/version metadata.
- §5 (`_Index.md` format) — so the skill can navigate.

Changes to those three sections may break external consumption.

---

## 9. What the LLM should NOT do

- Don't invent capability claims about models, tools, or CLI flags. If unsure, say so and add a `> [!warning] Unverified` callout.
- Don't silently overwrite a note during ingest. Mental model: add, refine, flag — don't erase.
- Don't write to `raw/` or `assets/` (except adding generated assets when explicitly requested).
- Don't skip the `log.md` entry on substantive operations.
- Don't rename notes without grepping for inbound wikilinks first.
- Don't introduce new top-level folders without proposing the change.
- Don't paste large URLs inline; route them through [[Resources]].
