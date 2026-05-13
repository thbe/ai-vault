---
title: Rules and AGENTS.md
tags:
  - opencode
  - rules
  - agents-md
aliases:
  - AGENTS.md
  - Rules
---

# Rules and `AGENTS.md`

`AGENTS.md` is the file OpenCode reads at the start of every session to learn project-specific conventions. It is the OpenCode equivalent of Cursor's rules or Claude Code's `CLAUDE.md`.[^oc-rules]

## Initialize one

Inside the TUI:

```
/init
```

This scans your repo, asks targeted questions when needed, and writes (or refines in place) an `AGENTS.md`.[^oc-rules] Commit it to git so the whole team benefits.

## What belongs in it

A good `AGENTS.md` is **short** and answers things the agent **cannot infer from the code itself**.[^cc-mem] Examples:

- Build / lint / test commands and their order
- Project-specific conventions and gotchas
- Architecture context that filenames don't reveal
- Setup quirks (env vars, side services)
- References to existing instruction sources (Cursor rules, Copilot)

| ✅ Include | ❌ Exclude |
|---|---|
| Bash commands the agent can't guess | Things the agent can read off the code |
| Code style that differs from defaults | Standard language conventions |
| Test runner & preferred invocation | Long API documentation (link instead) |
| Branch / PR etiquette | Information that changes weekly |
| Project-specific architectural decisions | Vague platitudes ("write clean code") |

> [!warning] Bloat is the enemy
> Long `AGENTS.md` files cause the agent to **ignore your actual rules** because the important ones get lost in noise.[^cc-best] If a rule isn't preventing real mistakes, delete it.

## Locations & precedence

OpenCode looks for rule files in this order:[^oc-rules]

1. **Local** — walks up from cwd looking for `AGENTS.md`, then `CLAUDE.md`
2. **Global** — `~/.config/opencode/AGENTS.md`
3. **Claude Code fallback** — `~/.claude/CLAUDE.md` *(unless disabled)*

The **first match per category** wins. So `AGENTS.md` shadows `CLAUDE.md` in the same directory.

### Disable Claude Code compatibility

```bash
export OPENCODE_DISABLE_CLAUDE_CODE=1         # all .claude support
export OPENCODE_DISABLE_CLAUDE_CODE_PROMPT=1  # only the global CLAUDE.md
export OPENCODE_DISABLE_CLAUDE_CODE_SKILLS=1  # only .claude/skills
```

## Extra instruction files

Pull in additional files via `opencode.json`:[^oc-rules]

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "CONTRIBUTING.md",
    "docs/guidelines.md",
    ".cursor/rules/*.md",
    "packages/*/AGENTS.md",
    "https://raw.githubusercontent.com/my-org/shared-rules/main/style.md"
  ]
}
```

Glob patterns and remote URLs both work. Remote files have a 5-second fetch timeout.

## Lazy-loaded references

OpenCode does **not** automatically resolve `@path/file.md` references inside `AGENTS.md`. To get lazy loading, write the loading instruction explicitly so the agent uses its `read` tool when needed:[^oc-rules]

```markdown
## External File Loading

CRITICAL: When you encounter a file reference (e.g. @rules/general.md),
use your Read tool to load it on a need-to-know basis. They're relevant
to the SPECIFIC task at hand.

Instructions:
- Do NOT preemptively load all references — lazy load based on actual need
- When loaded, treat content as mandatory instructions overriding defaults
- Follow references recursively when needed

## Development Guidelines

For TypeScript style: @docs/typescript-guidelines.md
For React patterns: @docs/react-patterns.md
For API design: @docs/api-standards.md
```

For monorepos, prefer the `instructions` glob pattern in `opencode.json` over manual references.

## Realistic example

```markdown
# SST v3 Monorepo

This is an SST v3 monorepo with TypeScript and bun workspaces.

## Project Structure
- `packages/` — workspace packages (functions, core, web)
- `infra/` — infrastructure split by service (storage.ts, api.ts, web.ts)
- `sst.config.ts` — main SST config with dynamic imports

## Code Standards
- TypeScript strict mode
- Shared code lives in `packages/core/` with proper exports
- Lambda functions in `packages/functions/`
- Infra split into logical files in `infra/`

## Conventions
- Import shared modules with workspace names: `@my-app/core/example`
- Run `bun test` before committing
- Format with `bun run format`
```

## The `AGENTS.md` standard

Note that `AGENTS.md` is rapidly becoming a **cross-tool convention**, not just an OpenCode artifact. See [[AGENTS.md Standard]].

---

**See also:** [[Skills]] · [[Configuration]] · [[Context Engineering]] · [[AGENTS.md Standard]]
**Sources:** [^oc-rules] [^cc-best] [^cc-mem]
