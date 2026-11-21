---
name: build-load-assessment
description: Builds or extends a load assessment in LoadSense from a brief, schedule, drawing notes or spreadsheet. Use when the user says "build a load assessment", "set up the loads for", "add these loads to LoadSense", "create a new LoadSense project", or pastes a load schedule to enter.
---

# Build a load assessment in LoadSense

Load the `navigator` skill first for the UI map and golden rules.

## 1. Plan before clicking
Turn the brief into a structure table and show it to the user in one message:

| Section (supply/TX) | Group (building/area) | Load | Type | Inputs | Occupancy profile | Group CF |
|---|---|---|---|---|---|---|

- Type: area-based (m², lighting W/m², small power W/m²), discrete (qty × kW), heating (kW thermal), cooling, EV (chargers × kW), etc.
- Profile: pick from the library by name. Search `/profile` for candidates; if nothing fits, propose a new profile (ask before creating).
- Flag every assumption (benchmarks, power factor default 0.95, CFs) clearly as an assumption.
- Ask one question if a genuinely blocking input is missing; otherwise proceed with labelled assumptions.

## 2. Create or open the project
- Existing project: `/projects` → search → open.
- New project: **New Project** on `/projects`; fill name, category, description (include "assumptions labelled" if illustrative). Confirm the name with the user first.
- Record project ID, scenario and revision.

## 3. Enter the structure, top down
1. **Add Section** for each supply/transformer; rename via the name textbox.
2. **Add Group** within each section; rename; set Group Coincidence Factor if specified.
3. **Add Load** within each group; expand; fill fields; choose Occupancy Profile via the searchable combobox; confirm Group shows "Section → Group".
4. After each group, `find` the group row and read its "X kW / Y kVA" to verify entry before moving on.

Use `form_input` for text/number fields where it works; otherwise triple-click the field and type. Tab out of a field to commit.

## 4. Run and report
1. **Run Analysis**, wait, `get_page_text`.
2. Report: Gross / Diversified / Coincident (kW and kVA), peak time from Insights, suggested substation and utilisation from Grid Capacity Negotiator.
3. Chart: Pattern C (KPI bars) plus the 24-hour site profile if hourly data is available (see `chart-patterns.md`).
4. List open assumptions as loose ends.

## Guardrails
- Never delete existing loads to "clean up" without asking.
- Large schedules (>20 loads): enter one group, verify, show progress, then continue.
