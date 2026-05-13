---
title: AGENTS.md Standard
tags:
  - ecosystem
  - standards
  - agents-md
  - rules
aliases:
  - AGENTS.md
---

# The AGENTS.md Standard

> [!quote] Tagline
> "A README for agents."

`AGENTS.md` is a **simple, open format** for giving AI coding agents the project-specific context they need: build commands, test instructions, conventions, gotchas. It is plain markdown with no required schema. Adopted by **60,000+ open-source repositories** and supported by a growing list of agents — OpenAI Codex, Google Jules, Cursor, Aider, goose, OpenCode, Zed, Warp, GitHub Copilot, Devin, Junie, Windsurf, and others.[^agents-md]

It is now stewarded by the **Agentic AI Foundation** under the Linux Foundation.[^agents-md]

## Why a separate file from README?

| File | Audience | Content |
|---|---|---|
| `README.md` | Humans (especially first-time visitors) | What the project is, quickstart, contribution guide |
| `AGENTS.md` | AI coding agents | Build, test, conventions, gotchas — anything you'd tell a new teammate |

Splitting the audiences keeps READMEs concise and gives agents a *predictable* place to look — they no longer have to guess which of `CONTRIBUTING.md`, `DEVELOPING.md`, the `docs/` folder, etc., contains the canonical instructions.

## Anatomy

There are **no required fields**. It's just markdown. Common sections:

```markdown
# AGENTS.md

## Setup commands
- Install deps: `pnpm install`
- Start dev server: `pnpm dev`
- Run tests: `pnpm test`

## Code style
- TypeScript strict mode
- Single quotes, no semicolons
- Use functional patterns where possible

## Testing instructions
- Run `pnpm turbo run test --filter <project_name>` for one package
- After moving files: `pnpm lint --filter <project_name>`
- Add or update tests for changed code, even if not asked

## PR instructions
- Title format: `[<project_name>] <Title>`
- Always run `pnpm lint` and `pnpm test` before committing

## Security considerations
- Never log user PII
- Secrets only via environment, never committed
```

Other useful sections seen in real-world `AGENTS.md` files:

- Project overview / mental model
- Architecture diagram or directory map
- Deployment steps
- Commit message conventions
- Files / dirs the agent must NOT touch

## Nested files (monorepos)

In a monorepo, place an `AGENTS.md` inside each package or service. **The closest file to the edited file wins.** Agents walk up the tree until they find one. This lets each subproject ship its own tailored guidance without bloating a root-level mega-file. The OpenAI repo at time of writing carries 88 nested `AGENTS.md` files.[^agents-md]

## Conflict resolution

> [!info] Precedence rule
> The closest `AGENTS.md` to the edited file wins; **explicit user prompts override everything**.[^agents-md]

## Cross-tool compatibility

Each agent reads `AGENTS.md` with its own conventions:

| Tool | Behavior |
|---|---|
| OpenCode | Reads `AGENTS.md` natively; also walks `.opencode/`, `~/.config/opencode/`, plus `CLAUDE.md` for compat. See [[Rules and AGENTS.md]].[^oc-rules] |
| OpenAI Codex | Reads `AGENTS.md` directly |
| Claude Code | Originally `CLAUDE.md`; many projects symlink `AGENTS.md` ↔ `CLAUDE.md` |
| Aider | Configure with `read: AGENTS.md` in `.aider.conf.yml`[^agents-md] |
| Gemini CLI | Configure with `{"context":{"fileName":"AGENTS.md"}}` in `.gemini/settings.json`[^agents-md] |
| Cursor, Zed, Warp, Junie, Devin, Windsurf, … | Native or trivially configured support |

For migrating an existing `CLAUDE.md` or `.cursorrules` etc., a symlink is the universal fix:

```bash
mv CLAUDE.md AGENTS.md && ln -s AGENTS.md CLAUDE.md
```

## Why this matters strategically

Before `AGENTS.md`, every coding agent shipped its own proprietary instructions file:

- `CLAUDE.md` (Claude Code)
- `.cursorrules` (Cursor)
- `.aider.conf.yml` (Aider)
- `.github/copilot-instructions.md` (Copilot)
- *etc.*

This forced project maintainers to either pick a vendor or maintain N copies. `AGENTS.md` is the de-vendoring move — the same dynamic [[Model Context Protocol|MCP]] pulled off for tools, `AGENTS.md` is doing for instructions.

The combination of **AGENTS.md + MCP** = a portable contract between projects and agents:

- *AGENTS.md* says **"how to work in this project"** (instructions)
- *MCP* says **"what extra capabilities are available"** (tools)

Both are open. Both are vendor-neutral. Both shift power away from agent vendors and toward project maintainers.

## Practical recommendations

1. **Start small.** Setup, test, code style — three sections is enough to begin.
2. **Live document.** Update it when you discover the agent stumbling on something.
3. **Nest in monorepos.** Place specific guidance close to the code it governs.
4. **Reference don't duplicate.** Point to existing docs (`see docs/architecture.md`) rather than copying.
5. **Don't stuff it.** See [[Context Engineering]] — every line is loaded every turn.

## See also

- [[Rules and AGENTS.md]] — OpenCode's specific implementation
- [[Model Context Protocol]] — the sister-standard for tools
- [[Context Engineering]] — why size discipline matters

---
**Sources:** [^agents-md] [^oc-rules]
