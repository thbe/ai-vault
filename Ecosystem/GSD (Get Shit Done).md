---
title: GSD (Get Shit Done)
tags:
  - ecosystem
  - tool
  - methodology
  - spec-driven
  - context-engineering
last_verified: 2026-05-13
status: stable
aliases:
  - GSD
  - Get Shit Done
---

# GSD (Get Shit Done)

> [!abstract] TL;DR
> **GSD** is a light-weight meta-prompting / context-engineering / spec-driven-development system for agentic coding runtimes (Claude Code, **OpenCode**, Gemini CLI, Codex, Copilot, Cursor, Windsurf, Kilo).[^gsd-readme] It frames its purpose as fighting **"context rot"** — the quality degradation that happens as the AI's context window fills up — and packages a six-command workflow that pushes heavy work into fresh sub-agent contexts while keeping the user's main session lean. Distributed via `npx get-shit-done-cc`. MIT, by "TÂCHES".

GSD is not a paradigm shift — it's a **disciplined assembly** of techniques this vault already covers ([[Context Engineering]], [[Sub-Agents and Delegation]], [[Plan-Build-Verify Workflow]], [[LLM Wiki Pattern]]) into a turnkey package with concrete commands.

> [!tip] Used here
> This is part of the active local toolkit — installed and used in real projects, not just catalogued. Notes below reflect lived practice, not just the README. Local config conventions and lessons-learned will accrete over time; for now, the upstream contract is the source of truth.

## The six-command loop

| # | Command | What it does |
|---|---|---|
| 1 | `/gsd-new-project` | Questions → research → requirements → roadmap |
| 2 | `/gsd-discuss-phase [N]` | Capture implementation decisions (layouts, API shapes, error handling) **before** planning |
| 3 | `/gsd-plan-phase [N]` | Research + plan + verify, looped until plans pass |
| 4 | `/gsd-execute-phase <N>` | Run plans in parallel waves; each task → atomic commit |
| 5 | `/gsd-verify-work [N]` | Manual acceptance walkthrough; failures get diagnosed fix plans |
| 6 | `/gsd-ship [N]` | Open PR from verified phase work |

Outer loop: `/gsd-complete-milestone` → `/gsd-new-milestone`. Helpers: `/gsd-progress --next`, `/gsd-map-codebase` for greenfield-on-existing-code.[^gsd-readme]

## The three problems GSD claims to solve

GSD's pitch is that most agentic-coding setups fail on three axes — and the system addresses each:

### 1. Context bloat → fresh sub-agent contexts

Heavy work (research, planning, execution, verification) happens in **dedicated sub-agents** that each start with a fresh ~200k-token context. The user's main context stays at 30–40%.[^gsd-readme] This is a textbook implementation of the [[Sub-Agents and Delegation|fan-out-for-isolation pattern]]: parent retains intent, children absorb the noisy exploration.

### 2. No shared memory → persistent project-state artifacts

GSD maintains structured markdown files that survive session boundaries:[^gsd-readme]

| File | Holds |
|---|---|
| `PROJECT.md` | Vision |
| `REQUIREMENTS.md` | Scope |
| `ROADMAP.md` | Where you're going |
| `STATE.md` | Current position and decisions |
| `CONTEXT.md` | Per-phase implementation decisions |

> [!info] Recognise this?
> This is the **[[LLM Wiki Pattern]] applied to project state instead of knowledge.** Same compounding-artifact mechanic: the LLM owns and maintains the markdown; the human curates direction; sessions hand off cleanly because the artifact survives. Karpathy's pattern is for *knowledge*; GSD's is for *project state*. Both rest on the same insight — sessions are stateless, files are not.

### 3. No verification → explicit verify step + debug agents

`/gsd-verify-work` walks through what was built; failures get a **diagnosed fix plan** ready for re-execution rather than ad-hoc debugging.[^gsd-readme] This is the explicit Verify phase from [[Plan-Build-Verify Workflow]] codified into a command, with the diagnose-then-execute loop preserving plan/build separation.

## Configuration

Settings in `.planning/config.json` (configured during `/gsd-new-project` or via `/gsd-settings`).[^gsd-readme] Key dials:

| Setting | Effect |
|---|---|
| `mode` | `interactive` (confirm each step) or `yolo` (auto-approve) |
| Model profiles | `quality` / `balanced` / `budget` per agent role |
| `workflow.research` / `plan_check` / `verifier` | Toggle the optional quality agents |
| `parallelization.enabled` | Run independent plans simultaneously |

Per-runtime model overrides are supported. Full schema in upstream `docs/CONFIGURATION.md`.

## Runtime support & permissions stance

GSD installs into runtime-specific skill/agent directories — `~/.claude/skills/gsd-*/` for Claude Code, `~/.codex/skills/gsd-*/` for Codex, equivalents for OpenCode and others.[^gsd-readme] One install command (`npx get-shit-done-cc@latest`) handles all 15 runtimes via interactive or non-interactive flags.

> [!warning] Designed for skip-permissions
> GSD is explicitly built around `claude --dangerously-skip-permissions`: *"GSD is built for frictionless automation. Skip-permissions is how it's intended to run."*[^gsd-readme] This is a deliberate trade-off — the system's value depends on parallel waves of executors operating without interactive prompts. See [[Permissions]] for the underlying machinery and what you give up; if you want fine-grained gating per tool/path, GSD's flow is not the right fit unmodified.

## Where GSD sits relative to this vault

GSD is best thought of as **an opinionated bundle of techniques this vault already documents**, packaged for one-command install:

| GSD mechanism | Underlying concept |
|---|---|
| Fresh sub-agent contexts per task | [[Sub-Agents and Delegation]] |
| `STATE.md`, `CONTEXT.md`, `ROADMAP.md` artifacts | [[LLM Wiki Pattern]] (applied to project state) |
| `/gsd-discuss-phase` → `/gsd-plan-phase` → `/gsd-execute-phase` → `/gsd-verify-work` | [[Plan-Build-Verify Workflow]] |
| "Context rot" framing | [[Context Engineering]] |
| Six commands, one loop, runtime-agnostic | The [[AGENTS.md Standard|cross-tool standard]] ethos |

If you understand the underlying notes above, GSD reads as a competent assembly. If you don't, GSD will give you the practice but the *why* will stay opaque — read the underlying notes first.

## Quotables

> *"The complexity is in the system, not in your workflow."* — TÂCHES[^gsd-readme]

> *"Claude Code is powerful. GSD makes it reliable."* — GSD README[^gsd-readme]

## See also

- [[Sub-Agents and Delegation]] — the fresh-context-per-task mechanic GSD industrialises
- [[Plan-Build-Verify Workflow]] — the four-phase loop GSD codifies as six commands
- [[Context Engineering]] — "context rot" is the named version of what this discipline addresses
- [[LLM Wiki Pattern]] — same persistent-artifact insight, applied to knowledge instead of project state
- [[AGENTS.md Standard]] — same cross-runtime, install-once ethos
- [[Permissions]] — what `--dangerously-skip-permissions` actually opts out of

---
**Sources:** [^gsd-readme]
