# Module 1: Your First Bead

> **Goal:** Create one bead, sling it to a polecat, and watch the full loop from creation to closed.

---

## The Core Loop

Every Gas Town workflow reduces to this:

```
bd create → gt sling → polecat works → gt done → Refinery merges → bead closes
```

This module walks through that loop with a single bead.

---

## What We're Building

A Python file: `weatherly/config.py` — a simple configuration module that holds API settings and defaults. Small enough to complete in one polecat session, clear enough to have real acceptance criteria.

---

## Step 1: Create the Bead

```bash
bd create "Add weatherly config module" \
  -t task \
  -p P2 \
  --description "Create weatherly/config.py with API configuration and app defaults.

File should contain:
- API_BASE_URL = 'https://wttr.in'
- DEFAULT_FORMAT = 'j1' (JSON format)
- DEFAULT_UNITS = 'metric'
- REQUEST_TIMEOUT = 10 (seconds)
- A Config dataclass with location, units, and format fields

All values should be type-annotated. Add a module docstring." \
  --acceptance "- [ ] File exists at weatherly/config.py
- [ ] All constants defined with correct values and type annotations
- [ ] Config dataclass exists with location, units, format fields
- [ ] Module docstring present
- [ ] No import errors (python -c 'from weatherly.config import Config')"
```

**You'll get back something like:** `edi-001`

Take note of that ID — you'll need it.

---

## Step 2: Inspect the Bead

```bash
bd show edi-001
```

You should see the title, description, acceptance criteria, status (`open`), and `created_by` (your crew identity).

Also try:
```bash
bd list --json | jq '.[0]'   # see the raw structure
```

---

## Step 3: Check What's Ready

```bash
bd ready
```

This shows all beads with no unresolved blockers — things that can be picked up right now. `edi-001` should appear here since it has no dependencies.

---

## Step 4: Create a Convoy

Before slinging, create a convoy so you can track this work:

```bash
gt convoy create "weatherly config module" edi-001 --notify --human
```

**You'll get:** `hq-cv-abc` (or similar)

```bash
gt convoy list           # see your new convoy in the dashboard
gt convoy status hq-cv-abc   # see the bead linked to it
```

---

## Step 5: Sling to a Polecat

```bash
gt sling edi-001 YOUR_RIG_NAME
```

Replace `YOUR_RIG_NAME` with your actual rig name (from `gt rig list`).

**What just happened:**
- Gas Town allocated a polecat name from the pool (e.g., Toast)
- Created a git worktree at `~/gt/YOUR_RIG/polecats/Toast/`
- Started a Claude Code session in that directory
- Hooked `edi-001` to Toast
- Toast is now running the Propulsion Principle: `gt hook` → `bd mol current` → execute

---

## Step 6: Watch It Work

### See the polecat running
```bash
gt polecat list --rig YOUR_RIG_NAME
```

### Peek at what it's doing
```bash
gt peek Toast
```

### Watch the live activity feed
```bash
gt feed
```

You'll see events like:
```
[20:15:33] Toast hooked edi-001: Add weatherly config module
[20:15:34] Toast → in_progress
[20:15:47] Toast committed: feat: add weatherly config module
[20:15:48] Toast → gt done
```

### Watch your convoy update
```bash
gt convoy status hq-cv-abc
```

---

## Step 7: After Completion

When Toast is done:

1. `gt done` was called → branch pushed → submitted to merge queue
2. Toast self-nuked (session + sandbox destroyed)
3. Refinery picks up the MR → rebases → merges to main
4. Bead closes automatically
5. Convoy lands (all tracked beads closed) → you get notified

Check the results:
```bash
bd show edi-001           # status: closed
gt convoy list --all      # convoy: landed
git log --oneline -3      # see the commit with Toast's attribution
```

Look at that git log entry:
```
abc1234 feat: add weatherly config module
Author: YOUR_RIG/polecats/Toast <you@email.com>
```

The polecat's identity is in the git history permanently.

---

## 🔍 What Just Happened (Annotated)

```
bd create edi-001
│  └─ Creates bead in Dolt DB with your identity as created_by
│
gt convoy create → hq-cv-abc
│  └─ Creates convoy bead in town-level (hq-) beads
│     Links edi-001 via "tracks" relationship
│
gt sling edi-001 YOUR_RIG
│  └─ Witness allocates "Toast" from name pool
│     Creates worktree: ~/gt/RIG/polecats/Toast/
│     Spawns: tmux session "gt-RIG-Toast" with Claude Code
│     Sets env: BD_ACTOR=RIG/polecats/Toast, GT_ROLE=polecat, etc.
│     Hooks edi-001 → Toast (bead status: hooked)
│
Toast runs gt prime
│  └─ Reads env variables → knows it's Toast in RIG
│     Checks hook → sees edi-001
│     Propulsion: execute immediately
│
Toast works
│  └─ Implements weatherly/config.py
│     Commits with gt commit (uses GIT_AUTHOR_NAME=RIG/polecats/Toast)
│     If session cycles (compaction): Witness respawns, work is safe in worktree
│
Toast calls gt done
│  └─ Pushes branch polecat/Toast/edi-001 to origin
│     Creates MR bead in merge queue
│     Requests self-nuke (session exits, worktree removed, slot returned to pool)
│
Refinery merges
│  └─ Rebases on main → merges
│     Closes edi-001
│
ConvoyManager detects close (within 5 seconds)
   └─ Checks if hq-cv-abc has all beads closed
      If yes: marks convoy as landed, sends notification
```

---

## ⚠️ Common Mistakes at This Stage

**"The polecat isn't doing anything"**
Check `gt polecat list --rig YOUR_RIG` — is it in "Working" state? Run `gt peek Toast` to see its output. If it's stalled, `gt polecat nuke Toast` and re-sling.

**"I don't see the bead in bd ready"**
Check if the bead was created correctly with `bd show edi-001`. If it's already `hooked`, it's been picked up.

**"The convoy didn't close"**
Convoy auto-closes when ALL tracked beads close. If one is still open, check `gt convoy status hq-cv-abc` for which bead is outstanding.

---

## 📝 Key Commands Learned

```bash
bd create          # create a bead
bd show <id>       # inspect a bead  
bd ready           # what can be worked on right now
gt convoy create   # create a tracking convoy
gt convoy list     # dashboard of active convoys
gt convoy status   # detailed convoy view
gt sling           # assign bead to polecat
gt polecat list    # see active polecats
gt peek <name>     # watch polecat output
gt feed            # real-time activity stream
git log --oneline  # see attributed commits
```

---

## Next: [Module 2 — Dependency Chains →](module-02-dependency-chain.md)
