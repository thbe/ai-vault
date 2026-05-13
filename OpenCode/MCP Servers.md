---
title: MCP Servers
tags:
  - opencode
  - mcp
  - tools
  - integration
last_verified: 2026-05-13
status: stable
---

# MCP Servers

OpenCode connects to external tools and data sources via the **[[Model Context Protocol]]** (MCP). Once configured, MCP-provided tools appear alongside built-in tools and the LLM can call them by name.[^oc-mcp]

> [!warning] MCP costs context
> Every enabled MCP adds tool descriptions to the prompt on **every** turn. The GitHub MCP server alone is famously heavy. Enable only what you need; consider scoping per-agent.

## Configure

Define MCPs under the `mcp` key in `opencode.json`. Two transport types exist: **local** (stdio subprocess) and **remote** (HTTP).[^oc-mcp]

### Local

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {
    "mcp_everything": {
      "type": "local",
      "command": ["npx", "-y", "@modelcontextprotocol/server-everything"],
      "environment": { "MY_KEY": "..." },
      "enabled": true,
      "timeout": 5000
    }
  }
}
```

### Remote

```jsonc
{
  "mcp": {
    "context7": {
      "type": "remote",
      "url": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "{env:CONTEXT7_API_KEY}"
      }
    }
  }
}
```

## OAuth

Remote MCPs that require auth use OAuth automatically — OpenCode detects 401 responses, performs **Dynamic Client Registration (RFC 7591)** when supported, and stores tokens in `~/.local/share/opencode/mcp-auth.json`.[^oc-mcp]

```bash
opencode mcp auth <server-name>     # trigger / re-auth
opencode mcp list                   # status of all MCPs
opencode mcp logout <server-name>   # remove credentials
opencode mcp debug <server-name>    # diagnose connection / OAuth flow
```

To disable auto-OAuth (e.g., the server uses an API key in headers):

```jsonc
{
  "mcp": {
    "my-api-key-server": {
      "type": "remote",
      "url": "https://...",
      "oauth": false,
      "headers": { "Authorization": "Bearer {env:MY_API_KEY}" }
    }
  }
}
```

## Scoping per agent

To keep the global context lean, disable MCP tools globally and enable them only for the agents that actually use them:

```jsonc
{
  "mcp": {
    "github": { "type": "local", "command": ["..."], "enabled": true }
  },
  "tools": { "github*": false },
  "agent": {
    "release-manager": {
      "tools": { "github*": true }
    }
  }
}
```

MCP tool names are prefixed with the server name, so `"<servername>_*"` reliably scopes the whole server.[^oc-mcp]

## Common servers

| MCP | Purpose | Transport |
|---|---|---|
| `@modelcontextprotocol/server-everything` | Reference / testbed for protocol features | local (npx) |
| [Sentry](https://mcp.sentry.dev) | Issues & error data | remote + OAuth |
| [Context7](https://github.com/upstash/context7) | Up-to-date library docs lookup | remote |
| [Grep by Vercel](https://mcp.grep.app) | Code search across public GitHub | remote |
| GitHub MCP | Repos, PRs, issues | local — **heavy context cost** |

## Active stack on this machine

The MCPs currently wired into this OpenCode install. All four are local stdio subprocesses; gitnexus is a system binary, the other three are `npx`-launched.

```jsonc
{
  "mcp": {
    "chrome-devtools": { "type": "local", "command": ["npx", "-y", "chrome-devtools-mcp@latest"], "enabled": true },
    "filesystem-mcp":  { "type": "local", "command": ["npx", "-y", "@modelcontextprotocol/server-filesystem"], "enabled": true },
    "memory-mcp":      { "type": "local", "command": ["npx", "-y", "@modelcontextprotocol/server-memory"], "enabled": true },
    "gitnexus":        { "type": "local", "command": ["gitnexus", "mcp"] }
  }
}
```

### `chrome-devtools` — browser as a verification surface

Maintained by the Chrome DevTools team.[^mcp-chrome-devtools] Exposes the full DevTools surface to the agent: navigation, a11y snapshots, JS evaluation in page context, network/console inspection, **Lighthouse audits** (accessibility, SEO, best-practices), **performance traces with insight analysis**, heap snapshots, device/network/CPU emulation, file uploads, dialog handling.

> [!tip] Slot in workflow
> Most powerful as the **Verify** lever in [[Plan-Build-Verify Workflow]] for any frontend work — and the canonical companion to the `UI/UX Implementation` skill. An agent can build a UI, take a snapshot, run Lighthouse, read console errors, and self-correct without leaving the loop.

### `filesystem-mcp` — superset of built-in file ops

Official reference server.[^mcp-server-filesystem] Overlaps OpenCode's built-in Read/Write/Edit but adds: **multi-file batch read** (one call returns N files), **directory tree as JSON**, sized listings with sort, atomic moves with collision protection, recursive glob search, **media file** read (image/audio → base64). Allowed-directory sandboxing is enforced server-side.

> [!info] Why both?
> The built-in tools handle most cases. Reach for filesystem-mcp when (a) you need a structured tree view, (b) you want to read several files in one round-trip to save turns, or (c) you need to handle binary/media content.

### `gitnexus` — code knowledge graph

The most distinctive MCP in this stack.[^mcp-gitnexus] Builds an indexed graph of the codebase (functions, classes, methods, files, calls, imports, inheritance, route handlers, MCP tool definitions, communities/process flows) and exposes it through high-leverage tools:

| Tool | What it does |
|---|---|
| `query` | Hybrid BM25 + vector search ranking **execution flows** (call chains) by relevance |
| `context` | 360° symbol view: callers, callees, categorised refs, process participation |
| `impact` | Blast-radius analysis: who breaks if this changes, with risk classification (LOW / MEDIUM / HIGH / CRITICAL) |
| `cypher` | Raw Cypher queries against the graph (Kuzu schema) |
| `detect_changes` | Maps `git diff` hunks to affected processes and risk |
| `rename` | Graph-aware multi-file rename (high vs low confidence per edit) |
| `route_map` / `api_impact` / `shape_check` | API consumer/contract analysis with mismatch detection |

> [!tip] Why it matters
> This is **[[Context Engineering]] operationalised**. Instead of grepping and hoping, the agent retrieves precise, ranked, structurally-correct context. It also enables [[Sub-Agents and Delegation|sub-agents]] to pull narrow slices of code understanding without inheriting the parent's context.

> [!warning] Pre-change discipline
> `impact` and `api_impact` are the right tools to call **before** modifying any function with non-trivial fan-out. They surface what tests need to run, what consumers will break, and what response shapes downstream code assumes.

### `memory-mcp` — persistent knowledge graph across sessions

Official reference server.[^mcp-server-memory] An entity-relation knowledge graph the agent can write to and query: `create_entities`, `create_relations`, `add_observations`, `search_nodes`, `open_nodes`, `read_graph`. Persists across sessions as a single JSON file.

> [!info] Compare with [[LLM Wiki Pattern]]
> Both solve session-statelessness through a durable artifact. They differ sharply in mechanism and audience:
>
> | | `memory-mcp` | [[LLM Wiki Pattern]] |
> |---|---|---|
> | Format | In-memory KG, opaque JSON | Human-readable markdown |
> | Editor | LLM only (via tool calls) | LLM owns, human curates |
> | Discoverability | Search/open via tool | Index, wikilinks, file explorer |
> | Use | Episodic facts, agent-only memory | Durable knowledge for both human and LLM |
>
> They are complementary, not substitutes. The wiki holds curated knowledge that survives model churn; memory-mcp holds operational state the agent needs *right now*.

### Stack summary

| MCP | Primary role | Maps to |
|---|---|---|
| chrome-devtools | Verify-phase automation, perf, UI iteration | [[Plan-Build-Verify Workflow]] (verify), `UI/UX Implementation` |
| filesystem-mcp | Higher-leverage file ops (batch, tree, media) | tooling baseline |
| gitnexus | Precise code retrieval + impact analysis | [[Context Engineering]], [[Sub-Agents and Delegation]] |
| memory-mcp | Cross-session episodic memory | [[LLM Wiki Pattern]] (compare/contrast) |

> [!tip] Steer with [[Rules and AGENTS.md]]
> Instead of telling the user to write `use context7` every prompt, add a rule like:
> ```
> When you need to look up library docs, use the `context7` MCP.
> ```

## See also

- [[Model Context Protocol]] — the protocol itself, not OpenCode's binding
- [[Permissions]] — gate which MCP calls require approval
- [[Context Engineering]] — why MCP discipline matters

---
**Sources:** [^oc-mcp] [^mcp] [^mcp-chrome-devtools] [^mcp-server-filesystem] [^mcp-server-memory] [^mcp-gitnexus]
