---
title: Resources
tags:
  - meta
  - references
---

# Resources

Authoritative external sources cited across this vault. Notes reference these by footnote number.

## Primary — OpenCode

| # | Source | URL |
|---|---|---|
| [^oc-docs] | OpenCode — Intro | <https://opencode.ai/docs/> |
| [^oc-config] | OpenCode — Config | <https://opencode.ai/docs/config/> |
| [^oc-agents] | OpenCode — Agents | <https://opencode.ai/docs/agents/> |
| [^oc-skills] | OpenCode — Agent Skills | <https://opencode.ai/docs/skills/> |
| [^oc-rules] | OpenCode — Rules (AGENTS.md) | <https://opencode.ai/docs/rules/> |
| [^oc-mcp] | OpenCode — MCP Servers | <https://opencode.ai/docs/mcp-servers/> |
| [^oc-permissions] | OpenCode — Permissions | <https://opencode.ai/docs/permissions/> |
| [^oc-tools] | OpenCode — Tools | <https://opencode.ai/docs/tools/> |
| [^oc-providers] | OpenCode — Providers | <https://opencode.ai/docs/providers/> |
| [^oc-zen] | OpenCode Zen | <https://opencode.ai/docs/zen/> |
| [^oc-github] | OpenCode on GitHub | <https://github.com/anomalyco/opencode> |

## Primary — Modern AI Coding

| # | Source | URL |
|---|---|---|
| [^cc-best] | Anthropic — Best practices for Claude Code | <https://www.anthropic.com/engineering/claude-code-best-practices> |
| [^cc-how] | Anthropic — How Claude Code works | <https://code.claude.com/docs/en/how-claude-code-works> |
| [^cc-skills] | Anthropic — Claude Code Skills | <https://code.claude.com/docs/en/skills> |
| [^cc-mem] | Anthropic — CLAUDE.md (memory) | <https://code.claude.com/docs/en/memory> |

## Standards & Protocols

| # | Source | URL |
|---|---|---|
| [^mcp] | Model Context Protocol — Introduction | <https://modelcontextprotocol.io/introduction> |
| [^mcp-spec] | MCP Specification | <https://modelcontextprotocol.io/docs/learn/architecture> |
| [^agents-md] | AGENTS.md — open standard | <https://agents.md/> |

## Adjacent ecosystem (for [[Agentic Coding Paradigm]] context)

| # | Source | URL |
|---|---|---|
| [^aider] | Aider — AI pair programming | <https://aider.chat/> |
| [^cursor] | Cursor docs | <https://cursor.com/docs> |
| [^cline] | Cline (formerly Claude Dev) | <https://github.com/cline/cline> |

## MCP servers (active local stack)

| # | Source | URL |
|---|---|---|
| [^mcp-chrome-devtools] | `chrome-devtools-mcp` — official Chrome DevTools MCP server (browser automation, performance traces, Lighthouse audits) | <https://github.com/ChromeDevTools/chrome-devtools-mcp> |
| [^mcp-server-filesystem] | `@modelcontextprotocol/server-filesystem` — official reference filesystem MCP | <https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem> |
| [^mcp-server-memory] | `@modelcontextprotocol/server-memory` — official reference knowledge-graph memory MCP | <https://github.com/modelcontextprotocol/servers/tree/main/src/memory> |
| [^mcp-gitnexus] | `gitnexus` — code knowledge graph MCP (BM25 + vector hybrid query, impact analysis, Cypher access). Distributed as a binary. | <https://github.com/abhigyanpatwari/GitNexus> |
| [^qmd] | `qmd` — local hybrid (BM25 + vector) markdown search engine with CLI and MCP server. Companion to the [[LLM Wiki Pattern]] for vaults outgrowing a single index file. | <https://github.com/tobi/qmd> |

## Talks & essays

| # | Source | URL | Local copy |
|---|---|---|---|
| [^limits-of-ai-yt] | "The Limits of AI: Generative AI, NLP, AGI, & What's Next?" — YouTube explainer (IBM-style, narrator Jeff). DIKW pyramid framing; macro/micro goal split; RAG/MoE/chaining as hallucination mitigations. | <https://youtu.be/rBlCOLfMYfw> | `raw/transcripts/limits-of-ai-rblcolfmyfw.txt` (auto-captions) |
| [^simple-aufgabe-yt] | "Die simple Aufgabe, an der jede KI scheitert" — German YouTube explainer. Statelessness; distribution-bound reasoning; the Lucas-Penrose / Gödel argument (flagged by the source itself as "kontrovers diskutiert" — contested); AI-as-tool framing where human expertise becomes *more* decisive. Used here for explanatory framing of practical and theoretical limits, not as endorsement of Penrose's philosophical conclusion. | <https://youtu.be/x_ZhqjbgdAQ> | `raw/transcripts/simple-aufgabe-an-der-jede-ki-scheitert-x_zhqjbgdaq.txt` (auto-captions, German) |
| [^llm-wiki-gist] | Andrej Karpathy — "LLM Wiki" (gist, 2026). The pattern of LLM-maintained persistent wikis vs. one-shot RAG; three-layer architecture (raw sources / wiki / schema); ingest/query/lint operations; index.md + log.md; Memex lineage. This vault is an instance of the pattern. | <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f> | — |
| [^gsd-readme] | GSD ("Get Shit Done") — README on GitHub. Multi-runtime spec-driven workflow tool by TÂCHES; coined "context rot" as the named failure mode; six-command loop; persistent project-state artifacts; parallel-wave executor architecture; explicitly designed around `--dangerously-skip-permissions`. MIT, `npx get-shit-done-cc`. | <https://github.com/gsd-build/get-shit-done> | — |

---

> [!note]
> If a footnote appears in a note but isn't listed here, please add it. This file is the single source of truth for citations.

[^oc-docs]: <https://opencode.ai/docs/>
[^oc-config]: <https://opencode.ai/docs/config/>
[^oc-agents]: <https://opencode.ai/docs/agents/>
[^oc-skills]: <https://opencode.ai/docs/skills/>
[^oc-rules]: <https://opencode.ai/docs/rules/>
[^oc-mcp]: <https://opencode.ai/docs/mcp-servers/>
[^oc-permissions]: <https://opencode.ai/docs/permissions/>
[^oc-tools]: <https://opencode.ai/docs/tools/>
[^oc-providers]: <https://opencode.ai/docs/providers/>
[^oc-zen]: <https://opencode.ai/docs/zen/>
[^oc-github]: <https://github.com/anomalyco/opencode>
[^cc-best]: <https://www.anthropic.com/engineering/claude-code-best-practices>
[^cc-how]: <https://code.claude.com/docs/en/how-claude-code-works>
[^cc-skills]: <https://code.claude.com/docs/en/skills>
[^cc-mem]: <https://code.claude.com/docs/en/memory>
[^mcp]: <https://modelcontextprotocol.io/introduction>
[^mcp-spec]: <https://modelcontextprotocol.io/docs/learn/architecture>
[^agents-md]: <https://agents.md/>
[^aider]: <https://aider.chat/>
[^cursor]: <https://cursor.com/docs>
[^cline]: <https://github.com/cline/cline>
[^limits-of-ai-yt]: <https://youtu.be/rBlCOLfMYfw>
[^simple-aufgabe-yt]: <https://youtu.be/x_ZhqjbgdAQ>
[^llm-wiki-gist]: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
[^gsd-readme]: <https://github.com/gsd-build/get-shit-done>
[^mcp-chrome-devtools]: <https://github.com/ChromeDevTools/chrome-devtools-mcp>
[^mcp-server-filesystem]: <https://github.com/modelcontextprotocol/servers/tree/main/src/filesystem>
[^mcp-server-memory]: <https://github.com/modelcontextprotocol/servers/tree/main/src/memory>
[^mcp-gitnexus]: <https://github.com/abhigyanpatwari/GitNexus>
[^qmd]: <https://github.com/tobi/qmd>
