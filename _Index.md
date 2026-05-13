---
title: AI Documentation — Index
tags:
  - moc
  - index
aliases:
  - Home
  - MOC
  - Index
---

# AI Documentation Vault

> [!abstract] Purpose
> A curated knowledge base on the **OpenCode** AI coding agent and how **modern AI-assisted software engineering** actually works in practice. Designed as an [Obsidian](https://obsidian.md) vault — every note is linked, every claim is sourced.

This is a **Map of Content (MOC)**. Start here, then follow the wikilinks.

---

## 🧭 Start here

- [[About]] — what this vault is, how it's organized, conventions used
- [[Resources]] — every external source referenced across the vault

---

## 🛠 OpenCode

The terminal-first, open-source AI coding agent built by [Anomaly](https://anoma.ly).

- [[OpenCode Overview]] — what it is, why it exists, how it differs from competitors
- [[Installation]] — install paths, prerequisites, platform notes
- [[Configuration]] — `opencode.json`, precedence, managed settings
- [[Agents]] — primary agents, subagents, the built-ins, custom agents
- [[Skills]] — `SKILL.md` discovery, frontmatter, permissions
- [[Rules and AGENTS.md]] — project & global instructions, Claude Code compatibility
- [[MCP Servers]] — connecting external tools and data sources
- [[Permissions]] — fine-grained tool gating, ask/allow/deny

```mermaid
graph LR
    U[User] --> P[Primary Agent<br/>Build / Plan]
    P -->|@mention or auto| S[Subagents<br/>General / Explore / Scout]
    P -->|skill tool| K[Skills<br/>SKILL.md]
    P -->|MCP| M[External Tools<br/>GitHub / Jira / DB]
    P -->|reads| R[AGENTS.md / Rules]

    class P,S internal-link;
    class K,M,R internal-link;
```

---

## 🧠 Modern AI Coding

How the practice has evolved beyond autocomplete into autonomous, agentic workflows.

- [[Agentic Coding Paradigm]] — what changes when the AI can act, not just suggest
- [[Context Engineering]] — the single most important skill in the new stack
- [[Plan-Build-Verify Workflow]] — the four-phase loop that actually ships
- [[Sub-Agents and Delegation]] — parallelism, isolated contexts, fan-out patterns
- [[Common Failure Patterns]] — and how to recognize them early
- [[Practical Limits of AI]] — what LLMs can't do, and why the discipline above exists
- [[LLM Wiki Pattern]] — Karpathy's pattern for LLM-maintained knowledge bases (this vault is an instance)

---

## 🌐 Ecosystem

- [[Model Context Protocol]] — the "USB-C for AI" standard powering tool integrations
- [[AGENTS.md Standard]] — the emerging cross-tool convention for agent instructions

---

## 🗺 At a glance

| Topic | Folder | Notes |
|---|---|---|
| OpenCode product | `OpenCode/` | 8 |
| Methodology & patterns | `Modern AI Coding/` | 7 |
| Cross-tool standards | `Ecosystem/` | 2 |
| Meta | `/` (root) | 2 |

---

> [!info] Vault conventions
> - All internal links use `[[Wikilinks]]`.
> - External sources live in [[Resources]] and are footnoted in each note.
> - Frontmatter `tags` are the primary navigation aid besides this MOC.
> - Last verified against upstream docs: **2026-05-13**.
