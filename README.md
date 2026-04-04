# ⛽ Gas Town Workshop

A hands-on tutorial for learning Gas Town — the multi-agent orchestration layer for AI coding workflows.

## What You'll Learn

By the end of this workshop you'll know how to:

- Think in Gas Town's mental model (agents, beads, convoys, molecules)
- Create beads and run them through polecats
- Build dependency chains and parallel work lanes
- Use the full design-to-delivery pipeline (spec → plan → beads → swarm)
- Run a crew session with interactive formula workflows
- Read attribution data and audit what your agents did
- Recover from stalled polecats and broken states

## The Project: `weatherly` 🌦️

You'll build **weatherly** — a CLI weather dashboard in Python. It's small enough to finish in an afternoon but has enough structure to demonstrate everything Gas Town can do:

```
┌─────────────────────────────────────────────────────┐
│                    weatherly CLI                    │
├─────────────┬──────────────┬───────────────────────┤
│   fetcher   │   processor  │       display          │
│  (API call) │ (parse JSON) │   (terminal UI)        │
└─────────────┴──────────────┴───────────────────────┘
         ↑                              ↑
    Module 2-3                     Module 4-5
  (dependency                   (full pipeline)
     chains)
```

## Modules

| Module | Topic | Concept |
|--------|-------|---------|
| [0. Mental Model](docs/module-00-mental-model.md) | How Gas Town thinks | Agents, beads, convoys, molecules |
| [1. Idea to Beads](docs/module-01-idea-to-beads.md) | Brief → design → plan | The human layer before the first bead |
| [2. First Bead](docs/module-02-first-bead.md) | Create → sling → done | The core loop |
| [3. Dependency Chain](docs/module-03-dependency-chain.md) | B needs A | Sequential work |
| [4. Parallel Lanes](docs/module-04-parallel-lanes.md) | A, B, C at once | Concurrent work |
| [5. Convoy Launch](docs/module-05-convoy-launch.md) | Stage → launch → watch | Automated dispatch |
| [6. Full Pipeline](docs/module-06-full-pipeline.md) | `mol-idea-to-plan` + `shiny` | Design pipeline + structured execution |
| [7. Recovery](docs/module-07-recovery.md) | When it breaks | Stalled polecats, zombies |

## Setup & Reference

| Section | Contents |
|---------|----------|
| [Setup](docs/setup.md) | Full environment setup, shell integration, crew workspace, troubleshooting |
| [A. Quick Reference](docs/appendix-quick-reference.md) | All key commands in one place |
| [C. Common Errors](docs/appendix-common-errors.md) | Error messages + fixes |

## Prerequisites

- Gas Town installed (`gt --version` works)
- `bd` and `gt` CLIs in your PATH
- Claude Code authenticated (`claude --version` works)
- **Your own fork of this repo** under an org or account you can push to
- The `gt-sdlc` Claude Code plugin installed (see below)

### Install the gt-sdlc Plugin

This workshop uses the `gt-sdlc` slash commands for the brief → design → plan pipeline in Module 1. Install it once before starting:

Open Claude Code (run `claude` in your terminal), then run these slash commands from within it:

```
/plugin marketplace add https://github.com/danielscholl/claude-sdlc
/plugin install gt-sdlc
```

Verify by running `/gt-sdlc:brief` inside Claude Code — you should see the command prompt.

## Repo Ownership Assumption

This workshop assumes you **fork this repository into your own GitHub org or
personal account** and do the work there.

Why: the tutorial has you creating beads, running polecats, and merging code.
That means your Refinery and polecats need push access to the repo you're using.
If you just clone `claudiogarza/gastown-workshop`, you'll be able to read it but
not push changes back.

Recommended flow:

1. Fork `claudiogarza/gastown-workshop` to your own org/account
2. Clone **your fork**
3. Use that clone as the repo for the workshop
4. Let Gas Town push and merge against the fork you control

## Quick Start

This gets you from zero to **ready for the tutorial**. Once you've done this, jump straight into the mental model. If anything here doesn't match your environment, use the full [Setup guide](docs/setup.md).

```bash
# 0. Fork this repo to your own GitHub org/account, then clone your fork
# Example (personal account):
#   gh repo fork claudiogarza/gastown-workshop --clone
# Example (org):
#   gh repo fork claudiogarza/gastown-workshop --clone --org YOUR_ORG
#   cd gastown-workshop

# 1. Go to your Gas Town root
cd ~/gt

# 2. Enable Gas Town and shell integration (one-time setup)
gt enable
gt shell install
source ~/.zshrc

# 3. Bring up all services
gt up

# 4. Verify everything is running
gt status

# 5. Add a rig for your fork (pick your own rig name)
# Example:
#   gt rig add workshop git@github.com:YOUR_ORG_OR_USER/gastown-workshop.git \
#     --upstream-url https://github.com/claudiogarza/gastown-workshop
# If you prefer HTTPS instead of SSH, use the HTTPS clone URL for your fork.

# 6. Verify the rig exists
gt rig list

# 7. Verify native formulas exist
gt formula list
```

You're ready when:
- `gt status` shows mayor and deacon running
- `gt rig list` shows the rig you'll use for the workshop
- `gt formula list` includes `mol-idea-to-plan` and `shiny`
- your rig points at a repo you can push to (your fork, not the upstream workshop repo)

**→ [Start with Module 0: The Mental Model →](docs/module-00-mental-model.md)**

If you hit setup issues, detour here: **[Full Setup Guide →](docs/setup.md)**

---

> 💡 **Tip:** Each module has step-by-step commands to run. **Do them.** Gas Town only makes sense when you watch it move — reading without doing is half the value.
