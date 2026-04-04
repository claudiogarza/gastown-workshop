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
| [Setup](docs/setup.md) | Get your town running | `gt up`, rig setup, crew workspace |
| [0. Mental Model](docs/module-00-mental-model.md) | How Gas Town thinks | Agents, beads, convoys, molecules |
| [1. Idea to Beads](docs/module-01-idea-to-beads.md) | Brief → design → plan | The human layer before the first bead |
| [2. First Bead](docs/module-02-first-bead.md) | Create → sling → done | The core loop |
| [3. Dependency Chain](docs/module-03-dependency-chain.md) | B needs A | Sequential work |
| [4. Parallel Lanes](docs/module-04-parallel-lanes.md) | A, B, C at once | Concurrent work |
| [5. Convoy Launch](docs/module-05-convoy-launch.md) | Stage → launch → watch | Automated dispatch |
| [6. Full Pipeline](docs/module-06-full-pipeline.md) | `mol-idea-to-plan` + `shiny` | Design pipeline + structured execution |
| [7. Recovery](docs/module-07-recovery.md) | When it breaks | Stalled polecats, zombies |

## Appendices

| Appendix | Contents |
|----------|----------|
| [A. Quick Reference](docs/appendix-quick-reference.md) | All key commands in one place |
| [C. Common Errors](docs/appendix-common-errors.md) | Error messages + fixes |

## Prerequisites

- Gas Town installed (`gt --version` works)
- `bd` and `gt` CLIs in your PATH
- Claude Code authenticated (`claude --version` works)
- A rig configured (`gt rig list` shows at least one)
- **Your own fork of this repo** under an org or account you can push to

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

```bash
# 0. Fork this repo to your own GitHub org/account, then clone your fork
# Example:
#   gh repo fork claudiogarza/gastown-workshop --clone
#   cd gastown-workshop

# 1. Go to your Gas Town root
cd ~/gt

# 2. Bring up all services
gt up

# 3. Verify everything is running
gt status

# 4. Start with Setup
```
**→ [Start with Setup →](docs/setup.md)**

---

> 💡 **Tip:** Each module has step-by-step commands to run. **Do them.** Gas Town only makes sense when you watch it move — reading without doing is half the value.
