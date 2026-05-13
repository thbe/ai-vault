---
title: MCP Servers
tags:
  - opencode
  - mcp
  - tools
  - integration
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
**Sources:** [^oc-mcp] [^mcp]
