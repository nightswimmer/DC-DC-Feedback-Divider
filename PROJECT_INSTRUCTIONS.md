# DC-DC Feedback Divider — Project Instructions

This file is the starting-point context loaded into every Claude conversation
about this project. Keep it up to date at the end of each session.

## What this project is

A browser-based calculator for designing the resistor feedback divider in
adjustable switching DC-DC converters (TL431-, LM317-, and similar
feedback-pin-referenced regulators). The output voltage is set by a resistive
ladder between the converter output and ground, with the IC's feedback pin
tapped at a fixed internal reference voltage (Vref).

The tool automates the tedious search for standard E-series resistor values
that hit one or more desired output voltages as closely as possible.

## Key features

- **Multi-rail support** — one ladder can supply multiple switched output
  voltages; each Vout is selected by shorting progressively more resistor
  segments to ground.
- **Combinable E-series pools** — E12, E24, E48, E96, E192 can be toggled on
  independently and merged into a single search pool (duplicates removed).
  Pool spans decades 0.001 kΩ to 10000 kΩ (effectively 1 Ω to 10 MΩ).
- **R1 strategy** — the top resistor (above the FB tap) can be fixed to one
  standard value or searched within a kΩ range.
- **Four optimisation strategies**
  - **Average** — minimises mean error across all rails.
  - **Maximum** — minimises the worst-case rail error (strict tolerance).
  - **Sum of Squares** — penalises outliers more than the mean.
  - **Weighted Average** — per-rail weight inputs appear in this mode, so the
    user can prioritise specific rails.
- **Live SVG schematic** — shows the Vout power rail, resistor ladder, FB tap,
  per-rail switch (SW) nodes, GND symbol, and per-rail cards with target vs.
  actual voltage and error %. The worst-error card is highlighted.
- **Input validation** — colour-coded input borders (ok / warn / error) and
  inline messages. Validates: Vref > 0, Vout > Vref, no duplicate Vouts,
  fixed R1 must exist in the active pool, range min < max.
- **Persistence** — full UI state (Vref, R1 mode & values, series toggles, Vout
  list, weights, optimisation mode) is saved to `localStorage` and restored on
  load.
- **Theming** — automatic light/dark theme via `prefers-color-scheme`.
- **Responsive** — two-column layout collapses to single column ≤720 px.

## Architecture

- Single self-contained file: `DC-DC-Feedback-Divider.html`. No build step, no
  dependencies, no external assets. Open in any modern browser to run.
- All CSS and JS live inline in the HTML.
- No server, no analytics, no network calls.

## Algorithm (for reference when tweaking)

1. Build the resistor pool from the active E-series, deduplicated and sorted.
2. Enumerate R1 candidates (a single value in fixed mode; all pool values in
   range in range mode).
3. For each R1, compute the *ideal* (real-valued) segment sizes that would hit
   every target Vout exactly. Reject R1 if any ideal segment is non-positive
   (i.e. target Vout ≤ Vref) or non-monotonic.
4. For each segment, take the K=4 nearest pool values as candidates.
5. Exhaustively enumerate the K^n combinations (odometer-style index
   increment), score each with the selected optimisation mode, keep the best.
6. Re-score the per-rail actual voltages and errors for display.

Error tiers: green <0.1 %, yellow <0.5 %, red ≥0.5 %.

## Collaboration preferences (from CLAUDE.md)

- Read this file first.
- Ask for clarification when something is unclear.
- For questions about a bug or a feature: answer the question first; do not
  change code unless confirmed.
- Suggest modifications if something feels incomplete.
- Remind the user to push to GitHub before big updates or structural changes.
- At the end of a chat / before a commit: update this file and `README.md` to
  reflect the current state, then give an overview + a suggested commit
  name/description. The user does the commit.

## Current state (2026-04-19)

- Initial working version committed (commit `007c05e`).
- `DC-DC-Feedback-Divider.html` exists on disk but was still untracked as of
  the latest session — it is the full, working calculator described above.
- No known bugs at time of writing. Next commit should add the HTML file plus
  the refreshed `PROJECT_INSTRUCTIONS.md` and `README.md`.
