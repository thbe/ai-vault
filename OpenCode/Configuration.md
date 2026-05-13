---
title: Configuration
tags:
  - opencode
  - config
last_verified: 2026-05-13
status: stable
---

# Configuration

OpenCode is configured via **JSON or JSONC** files that are **merged**, not replaced. This is the most important thing to internalize about its config model.[^oc-config]

## Precedence (lowest → highest)

1. **Remote config** — fetched from `.well-known/opencode` (organizational defaults)
2. **Global** — `~/.config/opencode/opencode.json`
3. **Custom path** — `OPENCODE_CONFIG=/path/to/file`
4. **Project** — `opencode.json` in project root (or any ancestor up to the git worktree)
5. **`.opencode/` directories** — agents, commands, plugins
6. **Inline** — `OPENCODE_CONFIG_CONTENT` env var
7. **Managed file** — `/Library/Application Support/opencode/`, `/etc/opencode/`, `%ProgramData%\opencode`
8. **macOS managed preferences** — `.mobileconfig` via MDM (highest, not user-overridable)[^oc-config]

> [!important] Merge, don't override
> Non-conflicting keys from every layer are preserved. Only conflicting keys get overridden by higher-precedence layers. So a global `autoupdate: true` and a project `model: "..."` cleanly compose.

## Minimal example

```jsonc
// opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-5",
  "small_model": "anthropic/claude-haiku-4-5",
  "autoupdate": true
}
```

## Important keys

| Key | Purpose | See |
|---|---|---|
| `model` | Default model (`provider/model-id`) | [Providers][^oc-providers] |
| `small_model` | Cheap model for titles / compaction | [^oc-config] |
| `agent` | Define / override [[Agents]] | [[Agents]] |
| `permission` | Tool gating | [[Permissions]] |
| `mcp` | [[MCP Servers]] | [[MCP Servers]] |
| `instructions` | Extra rule files (globs, URLs) | [[Rules and AGENTS.md]] |
| `tools` | Enable / disable tools (deprecated; prefer `permission`) | [^oc-tools] |
| `formatter` / `lsp` | Code formatters & language servers | [^oc-config] |
| `compaction` | `auto` / `prune` / `reserved` token buffer | see [[Context Engineering]] |
| `enabled_providers` / `disabled_providers` | Allowlist / denylist providers | [^oc-config] |
| `share` | `manual` / `auto` / `disabled` for conversation sharing | [^oc-config] |
| `snapshot` | Enable per-session undo via internal git | [^oc-config] |

## TUI config

UI-specific settings live in a separate file: `~/.config/opencode/tui.json` (or project-local `tui.json`).[^oc-config]

```json
{
  "$schema": "https://opencode.ai/tui.json",
  "theme": "tokyonight",
  "scroll_speed": 3,
  "diff_style": "auto"
}
```

## Variable substitution

Two forms work inside config values:[^oc-config]

```json
{
  "model": "{env:OPENCODE_MODEL}",
  "provider": {
    "anthropic": {
      "options": { "apiKey": "{file:~/.secrets/anthropic-key}" }
    }
  },
  "instructions": ["{file:./custom-instructions.md}"]
}
```

- `{env:VAR}` — environment variable (empty string if unset)
- `{file:path}` — file contents; `~` and absolute paths supported

## Managed settings (enterprise)

Organizations can ship an immutable `opencode.json` via:

- **Linux:** `/etc/opencode/opencode.json`
- **macOS:** `/Library/Application Support/opencode/` or MDM `.mobileconfig` under the `ai.opencode.managed` PayloadType
- **Windows:** `%ProgramData%\opencode\`

Users cannot override these. Verify with `opencode debug config`.[^oc-config]

---

**See also:** [[Permissions]] · [[Agents]] · [[Skills]] · [[MCP Servers]]
**Sources:** [^oc-config] [^oc-providers] [^oc-tools]
