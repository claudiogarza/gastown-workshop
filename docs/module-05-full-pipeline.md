# Module 5: The Full Pipeline

> **Goal:** Use Gas Town's native formula workflows — `mol-idea-to-plan` for design and `shiny` for implementation — instead of manually creating beads. Experience how the full pipeline works end-to-end in a crew session.

---

## Two Workflows, One Pipeline

Gas Town ships with two key formulas that handle the full lifecycle:

```
Your idea (1 sentence)
         │
         ▼
┌─────────────────────────────────────┐
│  mol-idea-to-plan                   │  ← runs in crew session
│                                     │
│  intake:         structure PRD      │
│  prd-review:     6 polecats review  │  autonomous
│  human-clarify:  you answer Qs      │  ← your input
│  generate-plan:  6 polecats design  │  autonomous
│  plan-review:    5 polecats review  │  autonomous
│  human-approve:  you greenlight     │  ← your input
│  create-beads:   beads created      │  autonomous
└────────────────────────────────────-┘
         │ outputs: beads hierarchy with deps
         ▼
┌──────────────────────────────────────┐
│  gt convoy stage → launch            │  you do this
│                                      │
│  Polecats execute each bead using    │
│  the `shiny` formula:                │
│    design → implement → review       │
│           → test → submit           │
└──────────────────────────────────────┘
```

**Two human gates** in the whole process:
1. After PRD review — you answer clarifying questions
2. After plan review — you approve before beads are created

Everything else is autonomous.

---

## The `shiny` Formula: What Polecats Actually Do

Every bead a polecat picks up runs the `shiny` formula under the hood — "Engineer in a Box":

```
design     → think about architecture, write design doc
implement  → write the code following the design
review     → self-review: does it match the design? any bugs?
test       → write and run tests, fix regressions
submit     → final check, commit, push to feature branch
```

This is why polecats consistently produce better output than raw "write this code" instructions — they're following a structured workflow with acceptance criteria at each step. You can also sling `shiny` directly to a polecat for any single bead.

---

## Why This Runs in a Crew Session

The `mol-idea-to-plan` workflow has two interactive steps where it pauses and waits for you. A polecat can't do this — it's headless and ephemeral. A crew session is persistent and interactive — you attach to it, answer the questions, and the workflow continues.

---

## Step 1: Start Your Crew Session

```bash
# Start the tmux session with Claude Code inside
gt crew start claudio

# In a separate terminal, attach to it
gt crew at claudio
```

Once attached, you'll see a Claude Code prompt inside `~/gt/YOUR_RIG/crew/claudio/`. This is your crew agent — it has already run `gt prime` and knows its identity.

> 💡 **Two windows:** Keep two terminal windows open. Window 1: your normal shell for running `gt` commands. Window 2: attached to the crew session via `gt crew at claudio`. The crew session is where the interactive pipeline runs.

---

## Step 2: Sling `mol-idea-to-plan` to the Crew Session

From your **normal shell** (Window 1):

```bash
cd ~/gt
gt sling mol-idea-to-plan YOUR_RIG/crew/claudio \
  --var problem="Add a 5-day weather forecast to weatherly. Show each day's high/low temps, precipitation probability, and conditions summary. Should integrate with the existing display module and be triggered by a --forecast flag." \
  --var context="weatherly is a Python CLI that calls wttr.in API. Existing modules: config.py, models.py, parser.py, fetcher.py, display.py, cli.py, __main__.py"
```

Then **attach to the crew session** (Window 2) to watch and interact:

```bash
gt crew at claudio
```

---

## Step 3: The Autonomous Phases

The crew agent picks up the sling immediately (Propulsion Principle) and starts:

### intake — PRD Drafting (autonomous, ~2 min)
Agent reads your problem statement and structures it into a PRD draft:
```
Plans/prd-reviews/5-day-forecast/prd-draft.md
```
No input needed from you here.

### prd-review — 6-Legged Parallel Review (autonomous, ~5 min)
6 polecats spin up simultaneously, each reviewing from a different angle:
- Requirements completeness
- Gap analysis
- Ambiguity detection
- Feasibility assessment
- Scope definition
- Stakeholder impact

```bash
# In Window 1, watch them work:
gt polecat list YOUR_RIG    # see 6 polecats running
gt feed                      # watch review events
```

Results are synthesized into a consolidated question list.

---

## Step 4: Your First Gate — `human-clarify`

The crew session pauses and asks you questions synthesized from the 6-leg review:

```
Based on the PRD review, I need clarification on:

1. [AMBIGUITY] The --forecast flag: should it show forecast instead of 
   current weather, or in addition to it?

2. [SCOPE] The wttr.in API returns up to 3 days of forecast by default
   (format=j1). Should we use a different endpoint or add a days parameter?

3. [DESIGN] Should ForecastDay be a separate dataclass from WeatherData,
   or extend it? (affects models.py, parser.py, display.py)
```

**Answer each question in the crew session.** The agent captures your answers and continues.

---

## Step 5: The Plan Generation Phase

### generate-plan — 6-Dimensional Design (autonomous, ~8 min)

6 polecats design the implementation plan in parallel, each from a different dimension:
- API design
- Data modeling
- UX/output format
- Scale/performance considerations
- Security
- Integration with existing code

### plan-review — 5-Legged Review (autonomous, ~5 min)

5 polecats review the plan for:
- Completeness (does it cover the full spec?)
- Sequencing (are the phases in the right order?)
- Risk (what could go wrong?)
- Scope creep (is anything gold-plated?)
- Testability (can the ACs be verified?)

---

## Step 6: Your Second Gate — `human-approve`

The crew session presents the full implementation plan and asks:

```
Plan Summary:

Phase 1: Data layer
  • Add ForecastDay dataclass to models.py (no deps)
  • Add parse_forecast() to parser.py (depends on ForecastDay)
  
Phase 2: Display
  • Add display_forecast() to display.py (depends on ForecastDay)

Phase 3: Wire up  
  • Update cli.py with --forecast flag (no deps)
  • Update __main__.py to call forecast path (depends on parser + display)

Phase 4: Tests
  • Add test_forecast_parser.py (depends on ForecastDay + parser)
  • Add test_forecast_integration.py (depends on full app)

Dependency graph verified: 2 cycles checked, none found.
Parallelism: Phase 1 tasks run in parallel. Phase 3 tasks run in parallel.

─────────────────────────────────────────────────────
Do you approve this plan? (yes to create beads, no to revise)
```

Type **yes**. The crew agent proceeds to `create-beads`.

---

## Step 7: Automatic Bead Creation

The agent creates the full bead hierarchy:

```
✓ Created edi-010 [epic]  5-day-forecast
✓ Created edi-011 [task]  Add ForecastDay dataclass
✓ Created edi-012 [task]  Add parse_forecast() function
✓ Created edi-013 [task]  Add display_forecast() function
✓ Created edi-014 [task]  Update cli.py with --forecast flag
✓ Created edi-015 [task]  Wire forecast into __main__.py
✓ Created edi-016 [task]  Add forecast parser tests
✓ Created edi-017 [task]  Add forecast integration test

Dependency graph:
  edi-012 blocked by edi-011
  edi-013 blocked by edi-011
  edi-015 blocked by edi-012, edi-013, edi-014
  edi-016 blocked by edi-011, edi-012
  edi-017 blocked by edi-015

✓ All beads created. Ready for convoy stage → launch.
```

---

## Step 8: Stage and Launch

From Window 1:

```bash
cd ~/gt
gt convoy stage edi-010
```

You'll see the real wave output:

```
Wave   ID       Title                           Blocked By
───────────────────────────────────────────────────────────────
1      edi-011  Add ForecastDay dataclass        —
1      edi-014  Update cli.py --forecast flag    —
2      edi-012  Add parse_forecast()             edi-011
2      edi-013  Add display_forecast()           edi-011
3      edi-015  Wire forecast into __main__.py   edi-012, 013, 014
3      edi-016  Add forecast parser tests        edi-011, 012
4      edi-017  Add forecast integration test    edi-015

7 tasks across 4 waves (max parallelism: 2 in wave 1)
Convoy created: hq-cv-forecast (status: staged_ready)
```

Launch it:
```bash
gt convoy launch hq-cv-forecast
```

Then monitor:
```bash
gt convoy -i     # interactive dashboard
gt feed          # event stream
```

Walk away. The ConvoyManager handles waves 2, 3, and 4 automatically.

---

## The `shiny` Formula in Action

Each polecat picks up its bead and runs `shiny` steps:

```
[Wave 1] furiosa picks up edi-011 (ForecastDay dataclass)
  → design:    writes design doc for ForecastDay structure
  → implement: creates models.py additions
  → review:    checks against design doc
  → test:      writes/runs unit tests
  → submit:    commits, pushes branch

[Wave 1] nux picks up edi-014 (cli.py --forecast flag)  
  → same shiny loop, simultaneously
```

You can peek at any step:
```bash
gt peek YOUR_RIG/furiosa    # see what step furiosa is on
```

---

## Running `shiny` Directly on a Single Bead

For any single bead where you want the full design-implement-review-test-submit lifecycle:

```bash
gt sling shiny YOUR_RIG --var feature="edi-042" --var assignee="edinsights_ui/crew/claudio"
```

Or sling a bead to a rig and `shiny` applies automatically via `mol-polecat-work`:
```bash
gt sling edi-042 YOUR_RIG
# shiny runs automatically as part of mol-polecat-work
```

---

## Pipeline vs Manual: What You Gain

| | Manual (Modules 1-4) | Pipeline (Module 5) |
|--|---|---|
| Spec quality | Your first draft | 6-leg PRD review, you answer gaps |
| Implementation plan | You reason about it | 6-dimensional parallel design |
| Plan validation | Your gut | 5-leg review (completeness, risk, scope) |
| Bead descriptions | You write them | Generated from approved plan |
| Dependency graph | You declare it | Agent computes from plan phases |
| Polecat workflow | raw bead | structured: design→implement→review→test→submit |
| Human time required | High (write everything) | Low (answer 2 gates) |

---

## 📝 Key Commands Learned

```bash
gt crew start claudio          # start crew session
gt crew at claudio             # attach to it
gt formula list                # see all available formulas
gt sling mol-idea-to-plan YOUR_RIG/crew/claudio \
  --var problem="..." \
  --var context="..."          # run the full design pipeline
gt sling shiny YOUR_RIG \
  --var feature="X"            # run shiny directly on a bead
gt convoy -i                   # interactive convoy monitoring
```

---

## Next: [Module 6 — Recovery →](module-06-recovery.md)
