---
title: Agentic Coding Paradigm
tags:
  - methodology
  - agentic
  - paradigm
---

# The Agentic Coding Paradigm

For a decade, "AI in the editor" meant **autocomplete**: the model suggests, the human accepts. The agentic paradigm inverts this. The model **acts** — reads files, runs tests, edits code, calls tools, opens PRs — and the human **reviews and steers**.

This shift changes what the developer's job actually is.

## Three generations

| Era | Tool | Human role | AI role |
|---|---|---|---|
| 1. Autocomplete | Copilot 2021 | Author every line | Suggests next tokens |
| 2. Chat-in-IDE | Cursor, ChatGPT | Author with help | Answers, drafts snippets |
| 3. Agentic | OpenCode, Claude Code, Codex | Director / reviewer | Plans, edits, executes, verifies |

The cognitive load moves from **typing code** to **specifying intent, providing context, and validating output**.

## What "agentic" actually requires

A coding agent is "agentic" when all five of these are true:

1. **Tool use** — it can read, write, search, and execute, not just generate text
2. **Planning** — it can decompose a goal into ordered steps
3. **Self-correction** — it observes tool output and adjusts (test failed → fix → re-run)
4. **Memory of intent** — it persists project-level context (see [[Rules and AGENTS.md]])
5. **Bounded autonomy** — humans gate dangerous actions (see [[Permissions]])

Without all five it's "AI assisted", not agentic.

## What this means for developers

> [!important] The bottleneck moved
> It is no longer "how fast can I type?" It is **"how clearly can I specify, and how carefully will I review?"**

Concrete consequences:

- **Specs > snippets.** A precise issue or `AGENTS.md` rule now produces more value than a hand-written function.
- **Reviewing AI output is a core skill.** PR review tempo > authoring tempo.
- **Reproducibility matters more.** Tests, types, and CI are how you trust agent output without re-reading every line.
- **Context is currency.** See [[Context Engineering]] — the discipline of feeding the model exactly what it needs and nothing more.
- **Process beats prompting.** A good [[Plan-Build-Verify Workflow]] outperforms clever one-shot prompts.

## The OpenCode philosophy

OpenCode positions itself as **terminal-native, model-agnostic, open-source** — the developer keeps control of the runtime, the prompts ([[Rules and AGENTS.md]]), and the tools ([[MCP Servers]]). This is the inverse of vendor-locked agentic IDEs and reflects a broader industry pattern: as agents become more capable, the value moves *away* from the wrapper and *toward* the open instruction surfaces (rules, skills, MCP).[^oc-docs] [^agents-md]

## Common misconception

> [!quote] "AI will write the code for me."
> Reality: AI will *propose* code that compiles, mostly tests, and looks plausible. Whether it's *correct* — semantically, architecturally, security-wise — is your call. See [[Common Failure Patterns]] for what goes wrong.

## See also

- [[Context Engineering]]
- [[Plan-Build-Verify Workflow]]
- [[Sub-Agents and Delegation]]
- [[Common Failure Patterns]]
- [[OpenCode Overview]]

---
**Sources:** [^cc-best] [^cc-how] [^oc-docs] [^agents-md]
