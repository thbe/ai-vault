---
title: About this Vault
tags:
  - meta
---

# About this Vault

This vault documents two intertwined topics:

1. **OpenCode** — a specific open-source AI coding agent (terminal, desktop, IDE).
2. **Modern AI coding practice** — the patterns, workflows, and mental models that distinguish effective use of agentic coding tools from the older "autocomplete in an editor" paradigm.

The two belong together: OpenCode is one of the cleanest concrete implementations of the modern paradigm, and the modern paradigm is what makes tools like OpenCode useful.

## Audience

- Engineers evaluating or onboarding to OpenCode
- Teams establishing conventions for agent-assisted development
- Anyone trying to understand *why* agentic coding tools work the way they do

## How to read it

- Start at [[_Index]] (the MOC).
- Each note is short and self-contained. Cross-references are wikilinks.
- Diagrams use Mermaid; callouts use Obsidian's `> [!type]` syntax.
- External sources are listed once in [[Resources]] and referenced inline as footnotes.

## Conventions

| Element | Convention |
|---|---|
| Internal link | `[[Note Name]]` |
| Section link | `[[Note Name#Heading]]` |
| External link | Standard markdown `[text](url)` — but cite via [[Resources]] when authoritative |
| Code config keys | `inline code` |
| Source attribution | Footnote `[^n]` resolved in [[Resources]] |
| Tag namespace | `opencode/`, `pattern/`, `concept/`, `meta` |

## What this is **not**

- Not a tutorial — see the [official OpenCode docs](https://opencode.ai/docs/) for step-by-step setup beyond [[Installation]].
- Not a benchmark — model and tool capabilities change weekly.
- Not exhaustive — focus is on the durable concepts that won't expire next month.

## Maintenance

Each note's frontmatter could carry a `last_verified` date in the future. For now, the vault as a whole was verified against upstream sources on the date in [[_Index]].

---

See also: [[_Index]] · [[Resources]]
