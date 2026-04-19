# DC-DC Feedback Divider Calculator

A browser-based tool for designing the resistor feedback divider in adjustable
switching DC-DC converters (TL431, LM317, and similar feedback-pin-referenced
regulators).

In a typical adjustable DC-DC converter, the output voltage is set by a
resistive divider between the converter output and ground, with the IC's
feedback pin tapped at a fixed internal reference voltage (Vref). This tool
automates the tedious search for standard E-series resistor values that hit
one or more desired output voltages as closely as possible.

## Features

- **Multiple switched outputs from a single ladder** — each voltage level is
  selected by shorting progressively more resistor segments to ground.
- **Combinable E-series pools** — pick any combination of E12, E24, E48, E96
  and E192; values are merged and deduplicated into a single search pool
  spanning roughly 1 Ω to 10 MΩ.
- **Flexible R1 (top resistor)** — fix it to a specific standard value, or
  search within a kΩ range.
- **Four optimisation strategies**
  - *Average* — minimises the mean error across all rails.
  - *Maximum* — minimises the worst-case rail error.
  - *Sum of Squares* — penalises outliers more than the mean.
  - *Weighted Average* — per-rail weights let you prioritise specific rails.
- **Live SVG schematic** — Vout rail at the top, resistor ladder with R1
  highlighted, FB tap, per-rail switch (SW) nodes, and a card for each rail
  showing target vs. actual voltage and the error %. The worst-error rail is
  visually highlighted.
- **Input validation** — colour-coded borders and inline messages catch
  invalid values (non-positive inputs, outputs ≤ Vref, duplicates, R1 not in
  pool, min ≥ max, etc.).
- **State persistence** — all settings are saved to `localStorage`, so your
  last configuration reloads automatically.
- **Automatic light / dark theme** following your OS preference.
- **Responsive** — single-column layout on narrow screens.

## How it works

For every R1 candidate, the tool computes the *ideal* (real-valued) resistor
segments that would hit every target voltage exactly, then picks the K=4
nearest pool values per segment and exhaustively searches those combinations.
Each candidate combination is scored with the selected optimisation strategy
and the global best is displayed.

Error tiers in the UI: green <0.1 %, yellow <0.5 %, red ≥0.5 %.

## Usage

Open `DC-DC-Feedback-Divider.html` in any modern browser. No build step, no
dependencies, no server — everything is self-contained in a single HTML file.

1. Enter the IC's **Vref** (feedback reference voltage).
2. Enter one or more **desired output voltages**. A new empty slot is added
   automatically as you fill in the previous one.
3. Toggle the **E-series pools** you want to draw from.
4. Choose an R1 **mode** — Fixed (one value) or Range (search a kΩ range).
5. Choose an **optimisation mode**. In *Weighted Average*, a per-rail weight
   row appears below the outputs.
6. The best solution is rendered as a schematic on the right, with
   per-rail error chips.

## Repository layout

- `DC-DC-Feedback-Divider.html` — the application (self-contained).
- `PROJECT_INSTRUCTIONS.md` — context for Claude Code / Claude-assisted
  development sessions.
- `CLAUDE.md` — collaboration rules for Claude.
- `README.md` — this file.
