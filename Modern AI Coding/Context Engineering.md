---
title: Context Engineering
tags:
  - methodology
  - context
  - prompting
  - core-skill
---

# Context Engineering

> [!abstract] One-line definition
> **Context engineering** is the discipline of putting exactly the right information in the model's window — and keeping the wrong information out.

It is the single highest-leverage skill in agentic coding. Models do not fail because they're stupid; they fail because they were given the wrong context, too much context, or stale context.

> [!tip] Why this discipline exists at all
> LLM sessions are **stateless** — there is no persistent memory inside the model. What looks like memory is context re-injected on every turn (system prompt, conversation, retrieved files, tool output).[^simple-aufgabe-yt] This is a permanent architectural property, not something a future model release will fix. Context engineering is the practice of curating that re-injected payload deliberately. See [[Practical Limits of AI]] for the full list of limits this discipline compensates for.
>
> At vault / knowledge-base scale, the same discipline is what the [[LLM Wiki Pattern]] formalises — a persistent, LLM-maintained artifact instead of per-session prompt curation. Same humans-curate / LLM-bookkeeps split, applied to knowledge instead of conversation.[^llm-wiki-gist]

> [!info] Optional framing — the DIKW pyramid
> The classic **Data → Information → Knowledge → Wisdom** pyramid is a useful lens here.[^limits-of-ai-yt] Raw *data* (file bytes, API responses) becomes *information* once given context (filename, schema), becomes *knowledge* when interpreted (this code is the auth path), becomes *wisdom* when applied to a goal (therefore don't refactor it without reading the security policy). Context engineering is the deliberate work of shipping the model the **rung it actually needs** — usually knowledge, occasionally raw data, never wisdom (that's the human's job; see [[Agentic Coding Paradigm]]).

## The context economy

Every token in the window:

1. **Costs money** (input tokens are billed per call, every turn)
2. **Costs latency** (longer prompts = slower responses)
3. **Costs quality** — large windows degrade attention and recall ("lost in the middle")
4. **Costs the user's next turn** — a window crammed with stale tool output crowds out fresh instructions

A well-tuned agent session is not "as much context as possible" — it's the **minimum context that preserves intent**.

## The four context surfaces in OpenCode

| Surface | Loaded | Cost | Use for |
|---|---|---|---|
| [[Rules and AGENTS.md]] | Always, every session | Constant | Invariants — build commands, conventions, "do not edit X" |
| Conversation history | Always, growing | Linear ↑ | The active task |
| [[Skills]] (`SKILL.md`) | On demand by name | ~1 line until used | Procedures, formats, domain how-tos |
| [[MCP Servers]] tool descriptions | Always (descriptions) | Per-tool, accumulates | External integrations |

The art is choosing the *cheapest surface that still works*. A 200-line `AGENTS.md` with rarely-used niche guidance is wasteful; the same content as a [[Skills|skill]] costs ~1 line until the model needs it.

## Core principles

### 1. Right information

Provide only what the current task needs. Three failure modes:

- **Underspecified:** "Refactor this." → which one? what direction? what constraints?
- **Stale:** outdated requirements still pinned in the system prompt
- **Mis-aimed:** generic best practices when the project has explicit deviations

### 2. Right form

- **Code:** raw code beats summaries — the model can read it
- **Decisions:** prose with rationale beats code-only
- **Lists:** structured (markdown tables, frontmatter) beats prose paragraphs
- **References:** point to file paths instead of inlining when the agent has `read`

### 3. Right time

Lazy-load. Don't preload "in case it's useful." [[Skills]] embody this — the description is always loaded, the body only when the agent decides it's relevant.

### 4. Compress aggressively

Long sessions accumulate noisy tool output and dead exploration. Summarize closed sub-tasks into dense findings; discard the raw transcript.[^cc-best]

## The compression pattern

Anthropic's Claude Code best-practices recommend explicit checkpoints:[^cc-best]

1. After **research** — summarize: "We found X, Y, Z. Decision: do A."
2. After **planning** — summarize: "Plan is steps 1–5; entered phase Build."
3. After **implementation** — summarize: "Files A, B, C changed. Tests pass. Ready to verify."

Each summary becomes the new floor; everything before it can be dropped.

## Anti-patterns

> [!failure] The kitchen-sink AGENTS.md
> 800 lines of every convention, edge case, and historical decision. Loaded on every prompt. Costs ~3000 tokens before the user even types.
> **Fix:** move rarely-used content into [[Skills]].

> [!info] "Context rot" — the named failure
> [[GSD (Get Shit Done)]] popularised the term **context rot**: the quality degradation that happens as the AI's context window fills up over a long session.[^gsd-readme] It's a useful single-word handle for the cumulative effect of every anti-pattern below — stale tool output, dead exploration, conversation hoarding. The defence is the same: aggressive compression at natural boundaries (see *The compression pattern* above).

> [!failure] The mega-MCP loadout
> Twelve MCP servers enabled globally. Each contributes 5–50 tools. Tool catalog alone consumes 10K+ tokens.
> **Fix:** scope MCPs per-agent (see [[MCP Servers]]).

> [!failure] Conversation hoarding
> A 4-hour session where the model keeps re-reading the same 600-line file because it forgot what it learned three turns ago.
> **Fix:** explicit summaries, or start a new session with a curated handoff.

> [!failure] Pasted-in everything
> Dropping a 2000-line file into chat when the agent could have read just the relevant function.
> **Fix:** point to file paths, let `read` with `offset`/`limit` do the work.

## A heuristic budget

For a typical coding session:

| Bucket | Target |
|---|---|
| `AGENTS.md` + global rules | < 1000 tokens |
| MCP tool descriptions | < 2000 tokens |
| Active task code/files in context | < 8000 tokens |
| Conversation working memory | < 16000 tokens |
| **Total before user turn** | **< 30K tokens** |

Above this, expect quality degradation regardless of advertised window size.

## See also

- [[Skills]] — the lazy-loading mechanism
- [[Rules and AGENTS.md]] — the always-loaded surface
- [[Plan-Build-Verify Workflow]] — natural compression checkpoints
- [[Common Failure Patterns]] — what goes wrong when you ignore this
- [[Practical Limits of AI]] — the underlying reasons this discipline is load-bearing

---
**Sources:** [^cc-best] [^cc-how] [^cc-mem] [^oc-skills] [^limits-of-ai-yt] [^simple-aufgabe-yt] [^llm-wiki-gist] [^gsd-readme]
