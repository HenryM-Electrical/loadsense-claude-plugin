---
name: change-impact
description: "Captures LoadSense results before and after any edit and charts the difference inline. Use whenever Claude changes a load, profile, coincidence factor, solar, battery or scenario in LoadSense, or when the user asks \"what did that change\", \"show me the before and after\", \"what's the impact of\", or \"how much headroom do we gain\"."
---

# Before/after change impact

Load `navigator` first. This skill wraps every write in LoadSense.

## 1. Snapshot (before)
From `get_page_text` on the workspace, record:
- Project, scenario, revision
- KPI cards: Gross, Diversified, Coincident (kW), Solar
- Site Totals: Coincident Max Demand kW and kVA, Current (Coincident)
- Peak time (Insights)
- Grid Capacity Negotiator: per-section peak kVA, suggested substation, utilisation
- If present, Battery Debug hourly Demand and Adjusted columns
- If the change targets one section, set the 24-Hour Demand Profile selector to that section and note it

Keep the snapshot in the conversation as a small table so it survives multiple edits.

## 2. Make the change
Apply exactly what was agreed. One logical change at a time so impacts are attributable.

## 3. Run Analysis, then snapshot (after)
Same fields as step 1.

## 4. Report
- Lead with the outcome in one line: e.g. "New TX now 288 kVA, 10 kVA over the 278 kVA interim limit."
- Chart: Pattern C (KPI delta bars) always; Pattern B (hourly before/after line) when hourly data exists; Pattern D when a transformer limit is in play.
- One or two sentences on why: which load or hour moved.
- Offer the next lever as options (e.g. adjust CF, shift a profile, move a group to the other TX, add battery).

## Stacking changes
When the user iterates, keep a running log:

| # | Change | Coincident kW | Section kVA | Δ vs start |
|---|---|---|---|---|

Show it after each step so progress is visible.

## Undo
If the user dislikes a change, revert it to the recorded before-value and re-run; confirm the KPIs match the snapshot.
