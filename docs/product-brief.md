# Project Brief: weatherly

## Vision

Weather in your terminal, instantly. One command, zero config, zero context-switch.

## The Problem

You're heads-down in a terminal and you want to know if it's going to rain. Today that means Cmd-Tab to a browser, load a weather site, wait for ads and cookie banners, squint at a cluttered page, and Cmd-Tab back — all to answer a ten-second question. Existing CLI weather tools either demand setup (API keys, config files, location files) or dump ugly unformatted text. Neither respects the speed you came to the terminal for.

## What It Does

- Looks up current conditions and a short forecast for any city: `weatherly dallas`
- Auto-detects your location when run with no argument: `weatherly`
- Shows current temp, "feels like," wind, humidity, and today / tonight / tomorrow forecast
- Renders in a styled Rich console layout — readable, scannable, pleasant
- Defaults to Fahrenheit; `--celsius` flag switches units

### Example

```
$ weatherly dallas
Dallas, TX — 72°F, Partly Cloudy
Feels like 69°F · Wind 12mph SE · Humidity 48%

Forecast:
  Today     Partly cloudy, high 78°F
  Tonight   Clear, low 58°F
  Tomorrow  Sunny, high 82°F
```

## Who It's For

Developers and sysadmins who live in the terminal and want weather without leaving it.

## Success Looks Like

A single command returns a styled forecast in under two seconds, with no config file, no API key setup, and no prior run required.
