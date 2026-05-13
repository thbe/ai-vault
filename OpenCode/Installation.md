---
title: Installation
tags:
  - opencode
  - setup
---

# Installation

OpenCode runs on macOS, Linux, and Windows (WSL recommended).[^oc-docs]

## Prerequisites

1. A modern terminal emulator — WezTerm, Alacritty, Ghostty, or Kitty are recommended.[^oc-docs]
2. API keys for the LLM provider(s) you want — or sign up for [OpenCode Zen][^oc-zen] for a curated, pre-tested set.

## Install paths

> [!example] Quickest — install script
> ```bash
> curl -fsSL https://opencode.ai/install | bash
> ```

| Platform | Command |
|---|---|
| npm | `npm install -g opencode-ai` |
| Bun | `bun install -g opencode-ai` |
| pnpm | `pnpm install -g opencode-ai` |
| Yarn | `yarn global add opencode-ai` |
| Homebrew | `brew install anomalyco/tap/opencode` |
| Arch (stable) | `sudo pacman -S opencode` |
| Arch (AUR) | `paru -S opencode-bin` |
| Chocolatey | `choco install opencode` |
| Scoop | `scoop install opencode` |
| Mise | `mise use -g github:anomalyco/opencode` |
| Docker | `docker run -it --rm ghcr.io/anomalyco/opencode` |

> [!tip] Homebrew tap vs core formula
> The `anomalyco/tap` formula tracks releases more tightly than the Homebrew-core `opencode` formula.[^oc-docs]

## Windows

> [!warning] Use WSL
> The official recommendation is to run OpenCode inside [Windows Subsystem for Linux](https://learn.microsoft.com/windows/wsl/install) for best compatibility.[^oc-docs] Native Windows installs work via Chocolatey, Scoop, or npm, but Bun support on Windows is still in progress.

## Initialize a project

```bash
cd /path/to/project
opencode
```

Then inside the TUI run `/init` — this scans your repo and writes (or refines) an `AGENTS.md` file. **Commit it.** See [[Rules and AGENTS.md]].

## First model

Inside the TUI:

```
/connect
```

Pick a provider, paste an API key, you're live. For the curated path, choose `opencode` / [OpenCode Zen][^oc-zen].

## Next

- [[Configuration]] — make it yours
- [[Permissions]] — decide what it's allowed to do before you let it run unattended

---

**Sources:** [^oc-docs] [^oc-zen]
