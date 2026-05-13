---
title: LLM Wiki Pattern
tags:
  - methodology
  - knowledge-management
  - pattern
  - meta
last_verified: 2026-05-13
status: stable
---

# LLM Wiki Pattern

> [!abstract] TL;DR
> The **LLM Wiki Pattern** (Andrej Karpathy, 2026)[^llm-wiki-gist] is a way to use an LLM agent to maintain a **persistent, compounding knowledge base** of markdown notes, instead of doing one-shot RAG over raw documents. The human curates sources and asks questions; the LLM does all the bookkeeping — summarising, cross-referencing, filing, flagging contradictions. **This very vault is an instance of the pattern.**

## Why this is not RAG

Most LLM-over-documents setups (NotebookLM, ChatGPT file uploads, vanilla RAG) work like this: you upload sources, the model retrieves relevant chunks at query time, and synthesises an answer. **Nothing accumulates.** Ask a subtle question that needs five documents and the model rediscovers the connections from scratch — every time.

The wiki pattern inverts this: the LLM **incrementally builds and maintains** a structured collection of markdown files that sits *between* you and the raw sources. When a new source arrives the LLM doesn't just index it — it reads it, integrates it into existing pages, updates summaries, and flags where it contradicts what was already there. Knowledge is **compiled once and kept current**, not re-derived every query.[^llm-wiki-gist]

> [!quote]
> "Obsidian is the IDE; the LLM is the programmer; the wiki is the codebase."[^llm-wiki-gist]

## The three layers

| Layer | Owner | Mutability |
|---|---|---|
| **Raw sources** — articles, papers, transcripts, PDFs | Human curates | Immutable; LLM reads, never writes |
| **The wiki** — interlinked markdown notes | LLM owns | LLM writes, edits, refactors |
| **The schema** — `AGENTS.md` / `CLAUDE.md` | Co-evolved | Disciplines the LLM into being a *maintainer*, not a chatbot |

The schema file is what makes the difference. Without it the LLM behaves like a generic assistant — answering questions but not maintaining the artifact. With it, conventions (filenames, frontmatter, citation format, when to create vs update, what to log) become consistent enough that the wiki stays coherent over hundreds of sources.

## The three operations

1. **Ingest.** Drop a source in `raw/` → tell the agent. It reads → discusses takeaways → updates existing notes first → only creates new notes if no fit → updates the index → appends to the log. *A single source might touch 10–15 wiki pages.*
2. **Query.** Ask a question. The LLM reads the index → drills into relevant notes → synthesises an answer with citations. **Good answers can be filed back into the wiki** — comparisons, analyses, surprising connections shouldn't disappear into chat history.
3. **Lint.** Periodic health check: contradictions between pages, stale claims, orphan pages, missing cross-refs, concepts mentioned without their own page, gaps a web search could fill.

These are the same three operations our [[AGENTS|AGENTS.md §4]] enforces.

## Two special files

- **`index.md`** — content-oriented. A catalogue of every page, organised by category. The LLM reads it first when answering a query. At moderate scale (~100 sources, hundreds of pages) this works *without* embedding-based RAG infrastructure.
- **`log.md`** — chronological. Append-only. Consistent prefix (`## [YYYY-MM-DD] kind | title`) makes it grep-able with unix tools.[^llm-wiki-gist]

In our vault these are [[_Index]] and `log.md` (see [[AGENTS#5. _Index.md format]] and [[AGENTS#6. log.md format]]).

## This vault as an instance

Direct mapping from gist concepts to files in this repo:

| Gist concept | Here |
|---|---|
| Raw sources | `raw/` (transcripts, PDFs — immutable per [[AGENTS#2. Directory layout]]) |
| Wiki | `OpenCode/`, `Modern AI Coding/`, `Ecosystem/`, root meta notes |
| Schema | [[AGENTS]] |
| `index.md` | [[_Index]] (TitleCase + leading underscore so Obsidian sorts it first) |
| `log.md` | `log.md` (same convention) |
| Ingest / Query / Lint | [[AGENTS#4. Operations]] §4.1 / §4.2 / §4.3 |

Deviations worth knowing:

- **Filenames** are TitleCase with spaces (`Context Engineering.md`), not kebab-case — readability over URL-safety; this is a small curated MOC, not an ingest pipeline.
- **`_Index.md`** has a leading underscore so it pins to the top of the file explorer; the gist's plain `index.md` is functionally equivalent.
- **Four-bucket folder split** (`OpenCode/`, `Modern AI Coding/`, `Ecosystem/`, root) is our domain choice — the gist is intentionally agnostic about structure.
- **One single source-of-truth citations file** ([[Resources]]) — we treat it as canonical and footnote into it. The gist doesn't mandate this, but it falls out naturally from "no duplicated bookkeeping."

The vault is also **registered with the `llm-wiki` skill** (the OpenCode skill of the same name in `~/.config/opencode/skills/llm-wiki/`), so external agent sessions can consult this vault on AI-coding questions without losing the maintenance discipline.

## Why it works

The bottleneck in personal/team knowledge bases has never been the reading or the thinking — it's the **bookkeeping**. Updating cross-refs, keeping summaries current, noticing when a new source contradicts an old claim, maintaining consistency across dozens of pages. Humans abandon wikis because the maintenance cost grows faster than the value. **LLMs don't get bored, don't forget to update a cross-reference, and can touch 15 files in one pass.** The wiki stays maintained because the cost of maintenance is near zero.[^llm-wiki-gist]

The pattern is a direct descendant of **Vannevar Bush's Memex** (1945) — a private, curated knowledge store with associative trails between documents. Bush's vision was much closer to this than to what the web became. The piece he couldn't solve — *who maintains the trails* — is exactly what the LLM handles now.

## Optional tooling

The gist mentions these as "use if useful, ignore if not":[^llm-wiki-gist]

- **Obsidian Web Clipper** — browser extension that converts web articles to markdown for the `raw/` collection.
- **qmd** — local hybrid (BM25 + vector) markdown search engine with a CLI and an MCP server. Useful once the wiki outgrows the index file. <https://github.com/tobi/qmd>
- **Obsidian graph view** — best way to spot hubs, orphans, and the actual shape of the wiki.
- **Marp** — markdown-based slide decks generated from wiki pages.
- **Dataview** — Obsidian plugin that queries page frontmatter to build dynamic tables. Pairs well with structured `tags`/`status`/`last_verified` fields (see [[AGENTS#3.1 Frontmatter]]).
- **Git** — the wiki is just a markdown repo; you get history, branching, and review for free.

None are required. This vault currently uses none of them beyond plain Obsidian + git.

## See also

- [[Context Engineering]] — same discipline at session scale; the wiki pattern is what it looks like at *vault* scale (compounding artifact instead of per-session prompt curation).
- [[Agentic Coding Paradigm]] — humans-own-*what*/*why*, agents-own-*how* applied to knowledge work as well as code.
- [[Plan-Build-Verify Workflow]] — ingest's "discuss-then-write" is plan-then-build at the knowledge layer.
- [[Sub-Agents and Delegation]] — large ingest tasks (re-classify the whole vault, lint everything) are natural delegation candidates.
- [[GSD (Get Shit Done)]] — same persistent-artifact insight applied to *project state* (`STATE.md`, `CONTEXT.md`, `ROADMAP.md`) instead of knowledge.
- [[AGENTS]] — this vault's concrete instantiation of the schema layer.

---
**Sources:** [^llm-wiki-gist]
