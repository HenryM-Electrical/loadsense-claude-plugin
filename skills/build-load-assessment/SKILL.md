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

- Type: always use the most specific LoadSense load type for the plant (see "Load type mapping" below). Generic area-based (room) loads are for spaces with no dedicated type.
- Coincidence factors: follow the Engineered Diversity Method. Time-based profiles describe WHEN a load is used through the day; the Group Coincidence Factor describes the chance of those loads peaking at exactly the same moment across a project of scale. The two are complementary, not double counting. Typical Group CF 0.8 to 0.9; lower for groups with many apartments, where simultaneous occupancy at scale is less likely. Use the user's stated CF where given.
- Profile: pick from the library by name. Search `/profile` for candidates; if nothing fits, propose a new profile (ask before creating).
- Flag every assumption (benchmarks, power factor default 0.95, CFs, COPs, EV limits) clearly as an assumption.
- Ask one question if a genuinely blocking input is missing; otherwise proceed with labelled assumptions.

### Load type mapping
| Plant or space | LoadSense load type |
|---|---|
| Dwellings / apartments | **Apartment** type (never a room/area load). Small power and lighting density per the brief; 10 to 15 W/m² is typical for Passivhaus-standard apartments where heating sits elsewhere (e.g. a communal ASHP). |
| Offices, retail, general spaces | Area-based (room) load: floor area, lighting W/m², small power W/m² |
| Heat pumps, boilers, electric heating | Heating load (thermal kW, with COP for heat pumps) |
| Chillers, VRF cooling | Cooling load |
| EV chargers | EV load (chargers × rating), with load management set (below) |
| Kitchens, lifts, pumps, process plant | Discrete load (qty × kW) |

Before entering, read the Add Load form's type options with `read_page` and use the exact names offered; if a type above is not offered, use the closest and say so.

### EV load management
When an EV load has a Static Bank Limit and import headroom:
- Set the load management mode the brief asks for (e.g. Fair Share).
- Enable **Smart override** where the site has import headroom, unless the brief says otherwise. Smart override lets EV charging exceed the static bank limit, but only within the site import limit. Without it, EV demand is capped at the static limit and can be under-represented.
- Afterwards, check the EV Load Management panel (Presentation mode): Peak EV Demand vs Static Bank Limit, "Smart override on", and the hourly breakdown, and report them.

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
