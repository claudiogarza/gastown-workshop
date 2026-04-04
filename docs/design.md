# Design Document: weatherly

> **Purpose:** This document captures the design decisions made after reading the product brief and before writing the first bead. It answers the questions an agent can't answer for you — what data source, what shape, what boundaries.

---

## From Brief to Design

The product brief tells us *what* to build. This document answers *how it will work* — just enough to give an agent clear, unambiguous instructions.

---

## Key Decisions

### 1. Data Source: wttr.in

**Options considered:**
- OpenWeatherMap — free tier available, requires API key registration
- WeatherAPI — good data, requires API key
- wttr.in — free, no API key, returns clean JSON, designed for terminal use

**Decision:** wttr.in. Zero setup friction for users, no credentials to manage, and it was literally built for terminal consumption. Aligns perfectly with the brief's "zero config required" goal.

**Impact:** `API_BASE_URL = 'https://wttr.in'`

### 2. Response Format: JSON (j1)

wttr.in supports multiple output formats:
- Default: human-readable text (not parseable)
- `?format=j1`: structured JSON with current conditions + 3-day forecast
- `?format=j2`: extended JSON (more data than we need)

**Decision:** `j1` — enough data for current conditions and a short forecast, clean structure, easy to parse.

**Impact:** `DEFAULT_FORMAT = 'j1'`

### 3. Units: Fahrenheit Default

**Options considered:**
- Fahrenheit only — US-centric but simple
- Fahrenheit default + `--celsius` flag — covers everyone, zero config for US users
- Celsius default + `--fahrenheit` flag — international standard but inconvenient for primary user base

**Decision:** Fahrenheit as default, `--celsius` flag to switch. The primary user (terminal-dwelling devs in the US) gets zero friction; international users have a one-flag escape hatch.

**Impact:** `DEFAULT_UNITS = 'fahrenheit'`, CLI accepts `--celsius` flag

### 4. Network Timeout: 10 seconds

A CLI tool should fail fast if the network is unavailable. 10 seconds is:
- Long enough to not fail on slow/congested connections
- Short enough to not leave the user wondering if it hung

**Impact:** `REQUEST_TIMEOUT = 10`

### 5. Configuration Shape

The user-facing inputs are:
- `location` — required, the city or place to look up
- `units` — optional, `--celsius` flag overrides default Fahrenheit
- `format` — optional, override the API response format

These become the fields of a `Config` dataclass — a clean way to pass settings through the app without relying on global state.

---

## Architecture

```
weatherly/
  config.py     ← API settings + Config dataclass (constants, no logic)
  fetcher.py    ← HTTP call to wttr.in, returns raw JSON
  processor.py  ← Parse JSON into structured weather data
  display.py    ← Format and print to terminal
  cli.py        ← Entry point, argument parsing
```

**Data flow:**
```
cli.py
  └─ builds Config from args
  └─ fetcher.py → raw JSON from wttr.in
  └─ processor.py → structured WeatherData
  └─ display.py → printed output
```

Each module has one job. Dependencies flow in one direction: cli → fetcher → processor → display. This matters for the bead breakdown — each module is an independent unit of work.

---

## Bead Breakdown

With the architecture clear, the work decomposes naturally:

| Bead | Module | Depends On | Notes |
|------|--------|------------|-------|
| 1 | `config.py` | nothing | Constants + Config dataclass. Good first bead — no dependencies, clear acceptance criteria |
| 2 | `fetcher.py` | config | HTTP call, returns raw JSON. Depends on config for URL + timeout |
| 3 | `processor.py` | fetcher | Parses JSON into a dataclass. Depends on knowing what fetcher returns |
| 4 | `display.py` | processor | Formats WeatherData for terminal. Depends on processor's output shape |
| 5 | `cli.py` | all above | Wires everything together. Last bead — all others must land first |

**Why this order matters:** The dependency chain is real. You can't display data you haven't processed. You can't process data you haven't fetched. You can't fetch without config. Each bead has a clear "done" state and a clear input/output contract.

---

## What This Document Is Not

This is not a spec. It doesn't tell an agent exactly what to write line by line — that's the bead description's job. This document captures the *reasoning* behind the decisions so that when you write the bead, you're not making it up as you go.

---

## Next Step

With this design in hand, you're ready to write the first bead:

```bash
bd create "Add weatherly config module" -t task -p P2 --stdin << 'EOF'
Create weatherly/config.py with API configuration and app defaults.

File should contain:
- API_BASE_URL = 'https://wttr.in'
- DEFAULT_FORMAT = 'j1' (JSON format)
- DEFAULT_UNITS = 'fahrenheit'
- REQUEST_TIMEOUT = 10 (seconds)
- A Config dataclass with location, units, and format fields

All values should be type-annotated. Add a module docstring.
EOF
```

Every value in that bead description came from a decision in this document. That's the point.
