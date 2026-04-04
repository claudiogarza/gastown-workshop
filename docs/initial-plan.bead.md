# Bead Plan: weatherly

> **Source:** Generated from `docs/design.md`. Every value traces to a design decision.

---

## Wave 1 — Foundation (no dependencies)

```bash
bd create "Add config constants and Config dataclass" -t task -p P1 --stdin << 'EOF'
Create weatherly/config.py and update pyproject.toml.

weatherly/config.py:
  - API_BASE_URL: str = "https://wttr.in"
  - DEFAULT_FORMAT: str = "j1"
  - DEFAULT_UNITS: str = "fahrenheit"
  - REQUEST_TIMEOUT: int = 10
  - Config dataclass with fields: location (str), units (str, default DEFAULT_UNITS), format (str, default DEFAULT_FORMAT)

pyproject.toml:
  - Add "rich>=13" to [project] dependencies

All values type-annotated. Module docstring required.

Acceptance criteria:
  - `from weatherly.config import API_BASE_URL, Config` works
  - Config("dallas") creates instance with default units/format
  - pyproject.toml includes rich>=13
EOF
```

---

## Wave 2 — Data Layer (parallel, both depend on config only)

```bash
bd create "Add weather data fetcher" -t task -p P2 --stdin << 'EOF'
Create weatherly/fetcher.py.

Responsibilities:
  - fetch_weather(config: Config) → dict
  - GET {API_BASE_URL}/{config.location}?format={config.format}
  - Timeout: REQUEST_TIMEOUT seconds
  - Raise on HTTP errors (fail-fast, no retries)
  - Return parsed JSON as dict

Imports from weatherly.config: API_BASE_URL, REQUEST_TIMEOUT, Config

Acceptance criteria:
  - fetch_weather(Config("dallas")) returns a dict with "current_condition" key
  - Timeout of >10s raises an exception
  - HTTP 404 raises an exception
EOF
```

```bash
bd create "Add weather data processor" -t task -p P2 --stdin << 'EOF'
Create weatherly/processor.py.

Responsibilities:
  - process_weather(raw: dict, config: Config) → WeatherData
  - WeatherData dataclass with fields:
    - location: str (city name from response)
    - temp_f: int, temp_c: int
    - feels_like_f: int, feels_like_c: int
    - description: str (e.g. "Partly Cloudy")
    - wind_mph: int, wind_dir: str
    - humidity: int
    - forecast: list[ForecastDay] (today, tonight, tomorrow)
  - ForecastDay dataclass: label (str), description (str), high_f/high_c (int), low_f/low_c (int)
  - Extract values from wttr.in j1 JSON structure

Acceptance criteria:
  - process_weather(sample_json, Config("dallas")) returns WeatherData
  - WeatherData.location is a non-empty string
  - forecast has exactly 3 entries
EOF
```

---

## Wave 3 — Presentation (depends on processor)

```bash
bd create "Add Rich terminal display" -t task -p P2 --stdin << 'EOF'
Create weatherly/display.py.

Responsibilities:
  - display_weather(data: WeatherData, config: Config) → None
  - Print to stdout using Rich console
  - Format:
    Dallas, TX — 72°F, Partly Cloudy
    Feels like 69°F · Wind 12mph SE · Humidity 48%

    Forecast:
      Today     Partly cloudy, high 78°F
      Tonight   Clear, low 58°F
      Tomorrow  Sunny, high 82°F
  - Use config.units to choose F or C values from WeatherData
  - Rich Panel or styled text — readable, scannable, pleasant

Imports from weatherly.processor: WeatherData
Imports from weatherly.config: Config

Acceptance criteria:
  - display_weather(sample_data, Config("dallas")) prints formatted output
  - --celsius config shows °C values instead of °F
EOF
```

---

## Wave 4 — Entry Point (depends on all above)

```bash
bd create "Add CLI entry point" -t task -p P2 --stdin << 'EOF'
Create weatherly/cli.py and update pyproject.toml entry point.

Responsibilities:
  - main() function: parse args → build Config → fetch → process → display
  - Arguments:
    - location (positional, optional — auto-detect via wttr.in if omitted)
    - --celsius flag (sets units to "metric")
  - Error handling: catch network/HTTP errors, print friendly message, exit 1
  - Wire: cli → fetcher → processor → display

pyproject.toml:
  - Add [project.scripts] weatherly = "weatherly.cli:main"

Acceptance criteria:
  - `weatherly dallas` prints weather for Dallas
  - `weatherly` (no arg) prints weather for auto-detected location
  - `weatherly --celsius dallas` shows Celsius values
  - Network error prints friendly message, exits 1
EOF
```

---

## Dependency Graph

```
config ──┬── fetcher ──┐
         │             ├── display ── cli
         └── processor─┘
```

## Convoy Launch

Once all beads are created:

```bash
bd list                    # verify all 5 beads exist
gt convoy stage            # stage them into a convoy
gt convoy launch           # start execution
```

---

## Notes

- Fetcher and processor are Wave 2 (parallel) because they don't import each other
- Processor takes the raw dict from fetcher, but doesn't import fetcher — the dict is passed through cli
- Display depends on processor's WeatherData type, so it must be Wave 3
- CLI wires everything, so it's always last
