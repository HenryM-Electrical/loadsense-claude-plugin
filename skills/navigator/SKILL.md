---
name: navigator
description: "Core guide for operating LoadSense (loadsense.app) in Claude in Chrome. Use whenever the user mentions LoadSense, a LoadSense project, load assessment, occupancy profile, coincident or diversified demand, Run Analysis, scenarios, revisions, or asks Claude to do anything on loadsense.app. Load this before the workflow skills (build-load-assessment, interrogate-profiles, change-impact, tx-scenarios, review-load-assessment)."
---

# LoadSense Navigator

LoadSense is electrical loading and energy analysis software implementing the Engineered Diversity Method: time-based 24-hour occupancy profiles replace static diversity factors to produce After Diversity Maximum Demand. Operate it through Claude in Chrome with the user's own sign-in.

## Golden rules

1. **Read text, not pixels.** Prefer `get_page_text`, `find` and `read_page` over screenshots. Load, scenario and revision actions have accessible labels (e.g. `Delete load: Offices`, `Duplicate scenario: Baseline`): target them by label. Screenshots only to confirm layout or for the few unlabelled icons (section/group chevrons and kebabs, profile-card icons).
2. **Navigate by URL** where possible (see URL map). It is faster and more reliable than clicking through menus.
3. **Know where you are.** Before any edit, record: project name, active **scenario** (Scenarios button, named `Scenarios: {name}`), active **revision** (label on the Revisions button, e.g. "P03"). Quote these in every report back.
4. **Snapshot before you change.** Capture the KPI cards and Site Totals before editing anything, so every change can be charted as a before/after (see `change-impact`).
5. **Protect the baseline.** Do not edit the Baseline or the favourited scenario unless the user names it. For experiments, duplicate a scenario first or ask which one to use.
6. **Confirm destructive actions.** Ask before deleting a load, group, section, scenario or profile. Toggling a load/group off (power switch) is the reversible alternative; offer it.
7. **Run Analysis after edits** so KPI cards, worst-case month and Insights refresh. Wait 2 to 3 seconds, then re-read.
8. **Always visualise the change.** After an edit or profile query, render a compact inline chart (see `references/chart-patterns.md`).
9. **Use LoadSense vocabulary**: "right-size" not "optimise"; feature names exactly as shown in the UI. British English.
10. Results are indicative and must be checked by a competent engineer; never present them as signed-off design.

## Model of a project

```
Project
 └─ Scenario (Baseline, "No Battery", "TX Rebalancing", ...)  + Revision (P01, P02, P03 ...)
     ├─ Loading tab
     │   └─ Section  = a supply / transformer (e.g. "Existing TX", "New TX")
     │       └─ Group = a building or area (e.g. "Manor House"), optional Group Coincidence Factor (CF)
     │           └─ Load = area-based, discrete, heating, cooling, EV ... each with an Occupancy Profile
     ├─ Solar tab
     └─ Battery tab
```

Results: Gross (undiversified) → Diversified (group sum with CF) → Coincident (time-based, after solar/battery). The coincident figure is the headline ADMD.

## URL map

| Page | URL |
|---|---|
| Projects list (search, category filter, 9 per page) | `https://loadsense.app/projects` |
| Project workspace | `https://loadsense.app/projects/{id}` |
| Occupancy profile library | `https://loadsense.app/profile` |
| Single profile | `https://loadsense.app/profile/{id}` |
| Portfolio | `/portfolio` |
| Capacity Intelligence | `/capacity-intelligence` |
| Panel Designer | `/panel-designer` |
| Help Centre (signed-in users; short video guides with text step lists) | `/app/help`, a guide opens as `/app/help?guide={slug}` |
| Sandbox demo (no signup, safe to experiment) | `https://loadsense.app/sandbox/demo` |

Project IDs are not guessable: open `/projects`, type in "Search projects...", then click the card. Note the ID from the URL for reuse in the session.

## Fast data routes (use these first)

| Need | Fastest route |
|---|---|
| Hourly factors for a profile | `/profile/{id}` → **Export** tab → text table "Hourly Diversity Factors" (05:00 to 04:00) |
| Profile ID used by a load | Expand load → open Occupancy Profile combobox → each option has an "Open … in a new tab" link with `href=/profile/{id}` (read with `read_page` on the link ref). Press Escape to close without changing it. |
| Site totals | `get_page_text` on the workspace → "Site Totals" block |
| Per-section kVA by time band, substation size | "Grid Capacity Negotiator" block in page text |
| Hourly site demand (projects with battery) | Expand "Show Battery Debug Data" → table Hour / Demand / NetAfterPV / ExcessPV / Battery / Adjusted / SOC |
| Peak time and diversity commentary | "Insights" block in page text |
| Solar annual figures | Solar tab → "Annual Summary" text (MWh, kWh/kWp, peak month, CO2) |
| Solar monthly kWh | Solar tab → hover each Monthly Generation bar (tooltip only) |
| Battery schedule | Battery tab → 24 labelled buttons "Hour HH:00: charge/discharge/idle" |
| Battery performance | Battery tab → "Battery Summary" text (daily cycles, peak shaving kW and %) |
| Hourly EV demand | Presentation mode → expand "EV Load Management" → "Hourly Breakdown" text table |
| All four series at one hour | Presentation 24-Hour Demand Profile → hover the hour (tooltip) |
| Profile weekly/monthly factors | `/profile` → card kebab → Edit Profile → Next through Daily (Enter hourly values), Weekly and Monthly, read the inputs → **Cancel** |
| Browse profiles by ID | `/profile` (wait ~3 s) → cards are links `/profile/{id}`, newest first |

Presentation mode is a view toggle: switch back to **Analysis** when done so the user returns to their normal view. If Presentation and Analysis disagree on coincident demand, report the Analysis figure and flag the mismatch.

Detailed element-by-element map: `references/ui-map.md`. Chart recipes: `references/chart-patterns.md`.

## Session start checklist

1. `tabs_context_mcp`, then open a new tab (do not hijack the user's LoadSense tab unless asked).
2. Go to the project; wait ~3 s for the client-side app to render (blank page = still loading; wait and retry once).
3. `get_page_text` → record project, scenario, revision, KPI cards, Site Totals.
4. Tell the user in one line what you found, then proceed.

## When unsure how a feature works

Before guessing at an unfamiliar control, open the Help Centre (`/app/help`), click **Steps** on the matching guide and read the step list with `get_page_text`. Guides: Navigating the Application (getting started), Overview of Project Load Management and Analysis (Presentation tab), Understanding the Portfolio Insights Page, Analysing Load Assessments Using Sections and Groups, Guide to Adding Loads in Project, Guide to Generating a New Occupancy Profile (`?guide=new-occupancy-profile`, includes meter data upload and mapping), Guide to Conducting Load Analysis and Renewable Energy Integration. Follow the guide's steps, not memory.

If the user wants to practise, or has no account, work in the sandbox (`/sandbox/demo`) rather than a live project. If the user needs a human, point them to "Book Technical Support Session" on the Help Centre page.

## Escalation

If an element cannot be found after two attempts, the page structure has likely changed. Stop, screenshot, and tell the user which step failed and what the page showed, so the UI map can be updated.
