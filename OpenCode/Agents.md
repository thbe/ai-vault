---
title: Agents
tags:
  - opencode
  - agents
  - architecture
last_verified: 2026-05-13
status: stable
---

# Agents

Agents in OpenCode are **specialized AI assistants** with their own prompt, model, and permission profile. There are two kinds.[^oc-agents]

## Two types

### Primary agents

The agent you talk to directly. Cycle them with **Tab**. Ship with two:

| Agent | Mode | What it does |
|---|---|---|
| **build** | primary | Default. All tools enabled. For active development. |
| **plan** | primary | Restricted: edit & bash default to `ask`. Pure analysis & planning. |

### Subagents

Specialized assistants invoked **by primary agents** (auto, based on description) or by **`@mention`**. They run in their own session/context.

| Agent | What it does |
|---|---|
| **general** | Multi-step research, can edit files. Good for parallel work units. |
| **explore** | Read-only fast codebase exploration. Cannot modify. |
| **scout** | Read-only external dep / library research; clones into managed cache. |

There are also hidden system agents — `compaction`, `title`, `summary` — that the runtime invokes automatically.[^oc-agents]

```mermaid
graph TB
    U[User] -->|Tab cycles| Build
    U -->|Tab cycles| Plan
    Build -->|@mention or auto| General
    Build --> Explore
    Build --> Scout
    Plan --> Explore
    style Build fill:#2e7d32,color:#fff
    style Plan fill:#1565c0,color:#fff
```

## Why subagents matter

Subagents run in **separate context windows** and report back summaries. This is the single most powerful pattern for context management — see [[Context Engineering]] and [[Sub-Agents and Delegation]].

## Configure

Two equivalent ways: JSON in `opencode.json` or a Markdown file with frontmatter.

### JSON

```jsonc
{
  "agent": {
    "code-reviewer": {
      "description": "Reviews code for best practices and potential issues",
      "mode": "subagent",
      "model": "anthropic/claude-sonnet-4-5",
      "prompt": "{file:./prompts/review.txt}",
      "permission": { "edit": "deny" }
    }
  }
}
```

### Markdown

Place in `~/.config/opencode/agents/<name>.md` (global) or `.opencode/agents/<name>.md` (project). The filename becomes the agent name.

```markdown
---
description: Reviews code for quality and best practices
mode: subagent
model: anthropic/claude-sonnet-4-5
temperature: 0.1
permission:
  edit: deny
  bash: deny
---

You are in code review mode. Focus on quality, bugs, performance, security.
Provide constructive feedback without making direct changes.
```

## Key options

| Option | Notes |
|---|---|
| `description` | **Required.** Used both in UI and as the dispatch hint when primary agents pick subagents. |
| `mode` | `primary` · `subagent` · `all` (default) |
| `model` | Override the global model for this agent |
| `prompt` | Inline string or `{file:./path}` |
| `temperature` | 0.0–1.0 (defaults are model-specific) |
| `steps` | Cap on agentic iterations (cost/safety guardrail) |
| `permission` | See [[Permissions]] — fine-grained per-agent gating |
| `permission.task` | Glob-based control over which subagents this agent may invoke |
| `hidden` | Hide a subagent from `@`-autocomplete (still callable by other agents) |
| `disable` | Toggle off without deleting |
| `color` | Hex (`#ff6b6b`) or theme token (`accent`, `warning`, …) |

## Create one interactively

```bash
opencode agent create
```

Asks scope (global vs project), description, generates a starter prompt, and lets you select permissions.[^oc-agents]

## Use cases

- **Build agent** — full dev work
- **Plan agent** — architectural analysis without changes
- **Code reviewer** — read-only, security/perf/maintainability lens
- **Docs writer** — file ops, no shell
- **Security auditor** — read-only, vulnerability lens
- **Debugger** — bash + read for investigation

## Tool permissions: from `tools` to `permission`

The legacy `tools: { write: false }` syntax still works but is **deprecated**. New agents should use the `permission` field for both finer-grained control (e.g. per-bash-command globs) and consistency with the global permission model.[^oc-agents]

---

**See also:** [[Skills]] · [[Permissions]] · [[Sub-Agents and Delegation]]
**Sources:** [^oc-agents]
