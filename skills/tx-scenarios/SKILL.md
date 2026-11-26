---
name: tx-scenarios
description: "Rebalances loads between transformers (sections) and manages and compares scenarios in LoadSense. Use when the user mentions TX rebalancing, moving loads to the new or existing transformer, an interim supply limit, kVA headroom, substation sizing, creating or switching scenarios, or comparing scenarios."
---

# Transformer rebalancing and scenarios

Load `navigator` and `change-impact` first.

## 0. Ask first (one round, multiple choice)
Before proposing any rebalancing, ask these together in ONE multiple-choice question set (AskUserQuestion where available), offering the options you can see on the page:
1. Which buildings or groups **cannot move** between transformers, and which loads **cannot be capped** or switched off?
2. Which scenario has which limit (e.g. interim and final supply limits, in kVA)?
3. Should changes apply to **one scenario or several**?
4. Should figures **include spare capacity**, or be compared without it?
Do not recommend a move or cap that the answers rule out.

## Scenario hygiene
1. Open **Scenarios**; read the list (`read_page` on the dialog). The button label is the active scenario.
2. Working scenario:
   - If the user named one, switch to it.
   - Otherwise propose duplicating the current scenario with a descriptive name (e.g. "Interim 300kVA – Block A to Existing TX") and confirm before creating. Use `Duplicate scenario: {name}`, then `Edit scenario: {new name}` to rename.
4. Revisions are checkpoints (P01, P02 ...). Adding one freezes the current revision; only do it when the user asks, e.g. at an issue stage.
3. Never delete a scenario without explicit confirmation naming it.

## Rebalancing loop
1. Snapshot (per `change-impact`), including each section's peak kVA and peak HOUR from Grid Capacity Negotiator, the Spare Capacity % (and whether the figures include it), and the user's limit for each TX from step 0. Compare with limits excluding spare unless told otherwise.
2. Chart Pattern D: sections vs capacity. State the gap.
3. Rank candidate moves on calculated share, never on card figures:
   - For each load on the overloaded section, read its profile name and factor at that section's peak hour, then calculate **design kW × profile factor at peak hour × group CF** (÷ PF for kVA). Group cards show each group's own worst case, not its share.
   - EV loads: read the EV Load Management Hourly Breakdown at the peak hour instead; if the section sits at the import limit minus reserve, the engine is filling headroom and moving or reprofiling other loads may not lower the peak (see navigator ui-map).
   - Exclude anything step 0 ruled out.
   - Present the top 3 options with the calculated kVA relief and the knock-on to the receiving section at ITS peak hour.
3a. **Verify before recommending.** For any option worth more than about 10 kVA: in a duplicated scenario, switch the load(s) off (`Disable load: {name}`), Run Analysis, record the section peak, then switch them back on and Run Analysis again. Report the measured relief beside the calculated one; only report savings measured or calculated this way.
4. On approval, move with `Move load to a different group: {load}` on the load row (or change the load's **Group** combobox to "Target Section → Group"). For a whole group, move each load or use the group kebab if it offers a move.
5. Run Analysis, re-snapshot, chart Pattern C + D, report headroom per section.
6. Repeat until both sections are within limit or the user stops. Keep the running log table.

Other levers to offer, not just moves: Group Coincidence Factor, profile choice (e.g. managed EV), disabling a load in an interim scenario, and storage or generation:
- **Battery**: add one scoped to the overloaded section (Scope "Section: {name}"); set Capacity, Max Power, Charge Mode. With Time Set Points, set discharge on the hours around that section's peak and charge overnight; read back the 24 labelled hour buttons to confirm. Report Peak Shaving from Battery Summary.
- **Solar**: scope a PV system to the section; note that midday generation rarely touches an evening peak (check the peak hour first).

## Compare scenarios
- Click **Compare scenarios** in the Scenarios dialog and `get_page_text` the result.
- If the comparison view does not expose numbers as text, switch scenario by scenario, snapshot each (Gross, Diversified, Coincident, per-section kVA, suggested substation), and build the table yourself.
- Chart: grouped bars, one category per KPI, one series per scenario. Close with a one-line recommendation framed as options, noting which scenario is favourited.
