# LoadSense UI map

Mapped live on 21 Nov 2026 (three passes: workspace; Solar, Battery, Presentation and profile dashboard; accessibility labels and hourly profile inputs). Load-row, scenario and revision actions now carry accessible labels: target them by label with `find`, never by position. Section/group row chevrons and kebabs, and profile-card icons, are still unlabelled.

## Global left rail (top to bottom)
Logo (`/`), Projects, Occupancy Profiles, Portfolio, Capacity Intelligence, Panel Designer, Account. Bottom: Help, settings, dark-mode toggle.

## Projects list `/projects`
- Heading "Projects", button **New Project**.
- Textbox "Search projects..." (filters live), category filter combobox ("All categories": Residential, Large Residential, Commercial, Mixed Use ...).
- Cards are links: category chip, name, description, "Updated {date}". Hidden kebab button per card.
- Pagination: "Showing 1–9 of N", "Items per page" combobox, page buttons.

## Project workspace `/projects/{id}`

### Header row
| Control | Notes |
|---|---|
| Project name + kebab | project settings |
| **Scenarios** button | accessible name `Scenarios: {active scenario}` (e.g. "Scenarios: Baseline") |
| **Revisions** button | visible label shows the ACTIVE revision (e.g. "P01 Initial", "P03") |
| Editing / Analysis / Presentation | view mode toggle |
| Export PDF | report |
| **Run Analysis** | recalculates and saves worst-case month |

### Scenarios dialog (click Scenarios)
- Title "Scenarios" with **Add scenario**.
- One row per scenario: name button (click to switch), then labelled actions: `Favourite scenario: {name}` / `Set as favourite`, `Edit scenario: {name}`, `Duplicate scenario: {name}`, `Delete scenario: {name}` (disabled for Baseline and the favourite).
- **Compare scenarios** button at the bottom.
- Escape closes.

### Revisions dialog (click Revisions)
- **Add revision**: "A new revision freezes the current one and carries all scenarios forward." Treat as a checkpoint; confirm with the user before adding (it freezes the current revision).
- One row per revision (e.g. "P01 Initial · Current"); click to switch. `Rename revision: {name}`.
- Compare button: "Create a second revision to compare checkpoints" (disabled until two revisions exist).
- Escape closes.

### Tabs: Loading | Solar | Battery

### Electrical Loads panel (Loading tab)
Toolbar: **Find load**, **Collapse all**, **Add Section**, **Add Group**, **Add Load**.

**Section row** (transformer/supply): collapse chevron, editable name textbox (e.g. "Existing TX"), summary text "Design: X kW · Y kVA · Diversified: X kW · Coincident: X kW · N loads", enable switch, **Add Group**, kebab.

**Group row**: chevron, editable name textbox, "CF: 0.8" badge, enable switch, **Add Load**, kebab. Below: "X kW / Y kVA" and checkbox **Group Coincidence Factor** with a numeric value box.

**Load row** (collapsed): chevron, type icon, name, summary (e.g. "5000 m² @ 12/20 W/m²", "1 × 500 kW", "20 chargers @ 7 kW", "200 kW thermal"), kW (blue), "/ kVA". Right-hand actions, all labelled with the load name:
- `Disable load: {name}` (green power icon; reversible; label flips to enable when off)
- `Move load to a different group: {name}`
- `Duplicate load: {name}`
- `Delete load: {name}` (red) → always confirm with the user first
Use `find` with the exact label, e.g. "Duplicate load: Offices".

**Load expanded** (area-based example):
Description · Floor Area (m²) · Flat Diversity Factor (checkbox) · Lighting (W/m²) · Small Power (W/m²) · Quantity · Power Factor · **Occupancy Profile** (searchable combobox) · **Group** (combobox "Section → Group").
Other load types show their own fields (rating, count, thermal kW, COP etc.): read the expanded form with `read_page` before editing.

**Occupancy Profile combobox**: search box "Search occupancy profiles"; "No profile"; section "Profiles used in this project" then "Other profiles"; each option has an "Open {name} in a new tab" link (`/profile/{id}`).

### Site Totals block
Total Design Demand · Total Apparent Power · Diversified Max Demand · Diversified Apparent Power · Coincident Max Demand · Coincident Apparent Power · Current (Design) · Current (Coincident) · Spare Capacity % · Phase (Three Phase).

### Further blocks (scroll)
- Power Density & Energy Use Intensity (W/m², kWh/m²/a, toggles for EV, spare, net of solar & battery).
- Power Factor Correction (collapsible).
- **Grid Capacity Negotiator**: per-section peak kVA, time-band table (e.g. 06:00–08:00 284 kVA), Derating %, Max Size, Required Capacity, Suggested Substation, Utilisation %.

### Right column
- KPI cards: **GROSS** (undiversified), **DIVERSIFIED** (group sum with CF), **COINCIDENT** (worst-case month or after battery), **SOLAR** (MWh/yr).
- **24-Hour Demand Profile**: section selector ("Entire Site" or a section), kW/kVA switch, "Copy chart as image". Series: Gross Demand, Solar PV, Adjusted Demand, Battery SOC. "Show Battery Debug Data" expands an hourly table.
- **Load Breakdown by Type** bar chart (Rooms, Discrete, Heating, EV).
- **Insights**: peak kW and time, solar, battery, EV, HVAC, Portfolio Benchmark.

## Solar tab
Heading "Solar PV Systems": one PV system per scope; **Add another** adds an array.
System card: name "Solar PV 1" (pencil to rename), "· Group: Block 1", enable switch, delete (red).
Fields: **Scope** (combobox: "Whole site", "Section: {name}", "Group: {name}"), **System Size (kWp)**, **Location** (region, e.g. South East England), **Orientation** (e.g. South (S)), **Tilt Angle (°)**, **Shading Factor (0-1)**, **Performance Ratio (PR)**, **Latitude (°) - Optional Override**.
Annual Summary (text): Annual Generation MWh, Specific Yield kWh/kWp, Peak Month, CO2 Saved tonnes.
Monthly Generation bar chart: values only as hover tooltips ("Jun 43,287 kWh"). Hover each bar to read.
Typical Day Profile chart: month combobox (January ...), caption "Typical day: X kWh total generation"; hourly values via hover.

## Battery tab
Heading "Battery Storage Systems": one battery per scope, dispatching against its own demand profile; **Add another**.
Battery card: name, scope chip, enable switch, delete.
Fields: **Scope**, **Capacity (kWh)**, **Max Power (kW)**, **Charge Mode** (options: "Solar Only (charge from excess PV)", "Time Set Points (charge/discharge windows)").
Time Set Points: 24 hour buttons, fully labelled, e.g. `Hour 10:00: discharge`. Clicking cycles Charge → Discharge → Idle. **Clear all** resets. Read the whole schedule with `find`/`read_page`; to set an hour, click until the label shows the target state.
**Dispatch strategy**: "Even spread" or "Max power per window (advanced)". Advanced shows numeric kW boxes per charge and discharge window (e.g. 00:00–10:00). Empty/0 falls back to Max Power.
**Initial State of Charge (%)**.
Battery Summary (text): Capacity, Max Power, Daily Cycles, Peak Shaving kW (%). Charts: State of Charge Profile, Charge / Discharge Profile.

## Presentation mode (header toggle)
Client-facing view, no load editor. Switch back to **Analysis** when finished.
- KPI cards: COINCIDENT DEMAND ("After battery storage"), SOLAR GENERATION (MWh/y), PEAK SHAVING (kW).
- 24-Hour Demand Profile (Gross Demand, Solar, Adjusted Demand, Battery SOC). Hover gives a tooltip with all four values for that hour.
- Solar Utilisation (needs Run Analysis), Demand Reduction (% diversified vs total).
- Monthly Energy Breakdown: stacked bars Base Load / Heating / Cooling / Solar Generation / Battery Discharge; **Split** / **Net** toggle; Copy chart as image.
- Collapsible **EV Load Management ({n} chargers)**: chargers, charger rating, load management mode (e.g. Fair Share), Static Bank Limit (A and kW), Peak EV Demand, "Smart override" flag, 24-hour EV chart, and a text **Hourly Breakdown** table (Hour / Active / Demand kW / EV Charging kW/ch).
- Collapsible **3D Energy Flow Visualisation**.
- The Coincident Demand card states its basis under the label (e.g. "Gross design basis · December"). Quote that basis with the number; a gross-basis figure is before battery adjustment, so it can differ from the after-battery coincident figure in Analysis.

## Occupancy profile library `/profile` (dashboard)
Header: **Add Templates**, **Import Meter Data**, **New Profile**. Search "Search profiles...". Category filter: All categories, Uncategorised, Education, Healthcare, Hospitality, Industrial, Large Residential, Mixed Use, Office, Other, Parks & Leisure, Residential, School, Transport.
Cards (newest first) are links to `/profile/{id}` once rendered (wait ~3 s; before that the tree shows no cards). Each card: name, "Category • Manual/Meter", sparkline, duplicate icon (unlabelled, first button in the card), kebab (unlabelled, second button: menu **Edit Profile**, **Share**, **Delete**), open chevron. "Items per page" plus page buttons.

### Profile editor (Edit Profile / New Profile): 4-step wizard, saves only on the last step
1. **Details**: Name, Category (+ "Add custom category").
2. **Daily**: base 24-hour curve (bars). Presets: Flat (0.5), Daytime, Evening, All Day (1.0). Expand **Enter hourly values** for 24 number inputs labelled `daily occupancy at HH:00` (00:00 to 23:00, values 0 to 1); typing updates the bars. Use `form_input` on each. READ the exact base curve here too.
3. **Weekly**: per day Mon–Sun a slider + text box (0–1) and **Customise** to give that day its own hourly curve.
4. **Monthly**: Jan–Dec text boxes (0–1). Final button **Save Changes**.
Navigation: **Next** / **Back** (the numbered step buttons do not jump ahead), **Cancel** or Close to discard. To READ weekly/monthly factors exactly, open Edit Profile, step through with Next, read the text boxes, then Cancel.

## Single profile `/profile/{id}`
Header: name, "Category • Source". Tabs:
- **Daily Profile**: average daily chart from 05:00 with 2-hour rolling trend.
- **Diversity**: "Diversity Curve", weighted weekly average of all day curves (raw multiplier). "View day" selector: All (base curve), Monday ... Sunday.
- **By Weekday**: "Energy Consumption by Day of Week", Bar/Heatmap toggle. Manual profiles show weekday scaling only; meter profiles show actual kWh.
- **By Month**: "Energy Consumption by Month", Bar/Heatmap. Manual = monthly scaling factors; meter = actual monthly totals.
- **Export**: "Hourly Diversity Factors" text table (05:00 → 04:00) + **Download CSV**. The only tab with numbers as text.
Export PDF top right.

## Help Centre `/app/help`
Title "Help Centre". Search box, sort ("Recommended"), category chips: All, Getting Started, Projects, Occupancy Profiles, Analysis & Renewables, Troubleshooting.
- **Interactive Sandbox Demo** card → panel with **Open Sandbox** (`/sandbox/demo`) and "Open in new tab". Sandbox covers sections and groups, every load type, duplicate/move/toggle/delete, Run Analysis, Solar PV, Battery, insights, presentation view, PDF reports.
- Guide cards: title, category, description, duration and step count, **Watch** and **Steps** buttons. Steps opens a text list of numbered steps with timestamps; the URL becomes `/app/help?guide={slug}`; "Copy link" gives a shareable link.
- **Still Need Help?** card → "Book Technical Support Session" (external booking page).

## Metrics that differ by design (not errors)
- **Solar share**: Insights gives the reduction in demand on a typical day (e.g. 5%); Presentation's Solar Utilisation gives the share of total annual site consumption met by solar (e.g. 14.5%). Different measures; do not report them as inconsistent.
- **Coincident demand**: gross design basis (before battery) vs after battery storage. Always quote which basis.
- **Demand Reduction** (Presentation): diversified vs total demand, not a battery or solar effect.

## What the cards and charts actually show
- **Group and load cards** show each item's own worst-case demand (its own peak hour and month), NOT its contribution at the site or section peak. The page notes this under the Presentation charts. Never read a card figure as a share of the peak; use the formula in the navigator skill.
- **Section hourly demand** is only shown in the 24-Hour Demand Profile chart: select the section, then hover each hour for the tooltip value. Record values in a table as you go.
- **Spare capacity**: the Spare Capacity % in Site Totals can be carried into transformer and substation figures. Check it before any comparison with a supply limit.

## EV load management engine
- With load management (e.g. Fair Share) and an import limit, the engine fills spare capacity up to the **import limit minus the reserve**. A section can therefore sit exactly at that cap: the cap, not the EV profile, sets the peak.
- Consequences: changing the EV profile may not lower the section peak, and can raise the site total by shifting charging into other hours. Test, do not assume.
- What EV actually draws each hour is in the engine's **Hourly Breakdown** table (Presentation → EV Load Management), not the EV group card.
- "No profile" on an EV load is NOT a flat full load as it is for other load types; the engine dispatches it. Read the Hourly Breakdown before describing it.
- Smart override lets EV exceed the Static Bank Limit, but only within the import limit.

## Practical notes
- After creating or editing a profile, **reload the project page** before assigning it; otherwise the new profile is missing from the Occupancy Profile dropdown.
