---
title: Permissions
tags:
  - opencode
  - permissions
  - safety
---

# Permissions

The `permission` config decides whether each tool call runs automatically, prompts the user, or is blocked outright. As of `v1.1.1` this replaces the legacy `tools: { foo: true }` boolean config (which still works for backwards compatibility).[^oc-permissions]

## The three actions

| Action | Effect |
|---|---|
| `"allow"` | Run without approval |
| `"ask"` | Prompt the user |
| `"deny"` | Block the call entirely |

## Shapes

**Single value** — apply to everything:

```json
{ "permission": "allow" }
```

**Per-tool** — set defaults and override:

```json
{
  "permission": {
    "*": "ask",
    "bash": "allow",
    "edit": "deny"
  }
}
```

**Granular (object syntax)** — most permissions accept a pattern map. Last matching rule wins, so put the catch-all first:[^oc-permissions]

```jsonc
{
  "permission": {
    "bash": {
      "*": "ask",
      "git *": "allow",
      "rm *": "deny"
    },
    "edit": {
      "*": "deny",
      "packages/web/src/content/docs/*.mdx": "allow"
    }
  }
}
```

### Wildcards

- `*` — zero or more of any character
- `?` — exactly one character
- everything else — literal match

## Available permissions

| Key | Matches against |
|---|---|
| `read` | file path |
| `edit` | file path (covers `edit`, `write`, `patch`) |
| `glob` | glob pattern |
| `grep` | regex pattern |
| `bash` | parsed command (e.g., `git status --porcelain`) |
| `task` | subagent type |
| `skill` | skill name |
| `lsp` | non-granular |
| `question` | asking the user a question mid-execution |
| `webfetch` | URL |
| `websearch` | query |
| `external_directory` | tool touches a path **outside** the working directory |
| `doom_loop` | the same tool call repeats 3× with identical input |

## Defaults

Permissive but with safety guards:[^oc-permissions]

- Most tools default to `"allow"`
- `doom_loop` and `external_directory` default to `"ask"`
- `read` defaults to `"allow"` **except** `.env` files:

```json
{
  "permission": {
    "read": {
      "*": "allow",
      "*.env": "deny",
      "*.env.*": "deny",
      "*.env.example": "allow"
    }
  }
}
```

## External directories

Use `external_directory` to allowlist paths outside the workspace. Home expansion (`~`, `$HOME`) works in patterns. **Note:** allowlisting a path here does not make it part of the workspace — other tool rules still apply.

```json
{
  "permission": {
    "external_directory": {
      "~/projects/personal/**": "allow"
    },
    "edit": {
      "~/projects/personal/**": "deny"
    }
  }
}
```

## What "ask" actually does

When prompted, the user sees three choices:[^oc-permissions]

- **once** — approve just this call
- **always** — approve future calls matching the suggested patterns *for the rest of this session*
- **reject** — deny

The "always" patterns come from the tool itself — bash, for example, typically suggests a safe command prefix like `git status*`.

## Per-agent overrides

Agent permissions are **merged** with the global config; agent rules win.[^oc-permissions]

```json
{
  "permission": {
    "bash": { "*": "ask", "git *": "allow", "git push *": "deny" }
  },
  "agent": {
    "build": {
      "permission": {
        "bash": { "*": "ask", "git commit *": "ask", "git push *": "deny" }
      }
    }
  }
}
```

Or in a markdown agent file:

```yaml
---
description: Code review without edits
mode: subagent
permission:
  edit: deny
  bash: ask
  webfetch: deny
---
```

## Practical recipes

> [!example] Read-only auditor
> ```json
> { "permission": { "*": "deny", "read": "allow", "glob": "allow", "grep": "allow" } }
> ```

> [!example] Sandboxed dev
> ```json
> {
>   "permission": {
>     "bash": { "*": "ask", "git status*": "allow", "git diff*": "allow", "npm test*": "allow" },
>     "edit": "ask"
>   }
> }
> ```

> [!example] Deny network
> ```json
> { "permission": { "webfetch": "deny", "websearch": "deny" } }
> ```

## See also

- [[Agents]] — per-agent permission overrides
- [[Skills]] — `skill` permission gating
- [[MCP Servers]] — `bash`/`webfetch` rules also apply to MCP-issued calls
- [[Common Failure Patterns]] — `doom_loop` is your safety net

---
**Sources:** [^oc-permissions]
