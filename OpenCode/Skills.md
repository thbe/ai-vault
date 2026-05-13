---
title: Skills
tags:
  - opencode
  - skills
  - reusability
last_verified: 2026-05-13
status: stable
---

# Skills

**Agent Skills** are reusable bundles of behavior — a `SKILL.md` file with YAML frontmatter — that OpenCode discovers automatically and loads **on demand** via the native `skill` tool.[^oc-skills]

The crucial property: **skills are not loaded into context until used.** They live as a one-line entry in the tool description (name + description), and their full body is fetched only when the agent decides it's relevant. This keeps token cost near-zero for skills that aren't being used in the current task.

## Discovery locations

Project-local (walked up from cwd to git worktree):
- `.opencode/skills/<name>/SKILL.md`
- `.claude/skills/<name>/SKILL.md` *(Claude Code compatibility)*
- `.agents/skills/<name>/SKILL.md`

Global:
- `~/.config/opencode/skills/<name>/SKILL.md`
- `~/.claude/skills/<name>/SKILL.md`
- `~/.agents/skills/<name>/SKILL.md`

## Frontmatter

Only these fields are recognized:[^oc-skills]

| Field | Required | Notes |
|---|---|---|
| `name` | ✅ | 1–64 chars, lowercase alphanumeric + single hyphens, must match the directory name. Regex: `^[a-z0-9]+(-[a-z0-9]+)*$` |
| `description` | ✅ | 1–1024 chars. Make it specific — it's how the agent decides whether to load. |
| `license` | – | Optional, free-form |
| `compatibility` | – | E.g. `opencode` |
| `metadata` | – | String→string map for arbitrary tags |

Unknown fields are ignored.

## Example

```markdown
---
name: git-release
description: Create consistent releases and changelogs from merged PRs
license: MIT
compatibility: opencode
metadata:
  audience: maintainers
  workflow: github
---

## What I do
- Draft release notes from merged PRs since the last tag
- Propose a SemVer bump
- Provide a copy-pasteable `gh release create` command

## When to use me
Use when preparing a tagged release. Ask before publishing if the
versioning scheme is ambiguous.
```

## How it surfaces to the agent

OpenCode injects an `<available_skills>` block into the `skill` tool description:

```xml
<available_skills>
  <skill>
    <name>git-release</name>
    <description>Create consistent releases and changelogs from merged PRs</description>
  </skill>
</available_skills>
```

Loading is a normal tool call:

```
skill({ name: "git-release" })
```

## Permissions

Pattern-based, in `opencode.json`:[^oc-skills]

```json
{
  "permission": {
    "skill": {
      "*": "allow",
      "internal-*": "deny",
      "experimental-*": "ask"
    }
  }
}
```

| Action | Behavior |
|---|---|
| `allow` | Loads immediately |
| `deny` | Hidden from the agent entirely |
| `ask` | Prompts the user before loading |

Override per agent in agent frontmatter:

```yaml
---
permission:
  skill:
    "documents-*": "allow"
---
```

Or kill the entire mechanism for one agent:

```yaml
---
tools:
  skill: false
---
```

When the `skill` tool is disabled, the `<available_skills>` block is omitted entirely.

## Skills vs Agents vs MCP

| Mechanism | Loaded when | Best for |
|---|---|---|
| **AGENTS.md / Rules** | Every session, always | Project-wide invariants, build commands, conventions |
| **[[Skills]]** | Lazy, by name | Specific procedures, formats, domain knowledge |
| **Subagents** ([[Agents]]) | Lazy, with isolated context | Heavy multi-step work that pollutes main context |
| **[[MCP Servers]]** | Always (tools listed), called as needed | External system integration |

> [!tip] Choose lazy when possible
> Anything that isn't needed every session belongs in a skill, not in `AGENTS.md`. See [[Context Engineering]] for the underlying principle.

## Troubleshooting

1. Filename must be exactly `SKILL.md` (caps).
2. `name` and `description` must be present.
3. `name` must match the directory.
4. Skill names must be unique across all discovery paths.
5. A `deny` permission hides the skill from the agent.[^oc-skills]

---

**See also:** [[Agents]] · [[Rules and AGENTS.md]] · [[Context Engineering]]
**Sources:** [^oc-skills] [^cc-skills]
