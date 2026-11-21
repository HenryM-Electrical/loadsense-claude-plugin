---
name: interrogate-profiles
description: "Reads, compares and charts LoadSense occupancy profiles and checks whether the right profile is applied to each load. Use when the user asks \"what profile is X using\", \"compare these profiles\", \"show me the heating profile\", \"is this profile right\", \"which loads drive the peak\", or wants a profile drafted or adjusted."
---

# Interrogate occupancy profiles

Load `navigator` first.

## Find what a load is using
1. In the workspace, use **Find load** or `find` to locate the load; expand it.
2. Read the **Occupancy Profile** combobox label.
3. To get the ID: open the combobox, `read_page` on the matching "Open {name} in a new tab" link → `href=/profile/{id}`. Press **Escape** to close without changing selection.
4. For an audit of every load: collapse-all, then expand each load in turn and build a table Load | Type | kW | Profile. Present the table.

## Read the numbers
- Open `/profile/{id}` in the same tab (or a second tab you created) → **Export** tab → `get_page_text`.
- Parse "Hourly Diversity Factors" (24 rows, starting 05:00). Reorder to 00:00 to 23:00.
- Also note header "Category • Manual/Meter" (meter-derived profiles carry more weight).
- Seasonality and weekends: By Weekday / By Month tabs show them as charts only. For exact factors, open the profile's **Edit Profile** wizard from the `/profile` card kebab (second button on the card), press **Next** to Daily (expand Enter hourly values for the base curve), Weekly (Mon–Sun text boxes, plus any "Customise"d days) and Monthly (Jan–Dec text boxes), read them, then **Cancel**. Never press Save Changes when only reading.
- Flag heating/cooling profiles whose monthly factors are all 1.00: they will not show winter/summer variation.
- Diversity tab "View day" selector shows an individual weekday's curve when days have been customised.

## Chart and judge
- Chart Pattern A: overlay the profiles in question (fraction of design load by hour).
- Then sanity-check against engineering intent and say so plainly, e.g.:
  - heating profile peaking mid-afternoon in winter → suspicious
  - kitchen profile flat 24 h → likely placeholder
  - binary 0/1 step profiles → coarse; may overstate coincidence at shoulders
- Name which loads coincide at the site peak hour (from Insights) and estimate each one's contribution = design kW × factor at that hour. Show as a small table, biggest first.

## Changing profiles (writes)
- Swapping the profile on a load: confirm load, old profile, new profile with the user (unless their request already said exactly this), snapshot KPIs, swap, Run Analysis, then hand over to `change-impact` for the before/after chart.
- Drafting a new profile: propose 24 hourly values plus weekday and monthly factors as a chart first; only create in `/profile` → **New Profile** after the user approves. Name it clearly with project prefix (e.g. "PRJ01 ASHP Heating – Winter").
  - Wizard: Details (name, category) → Daily → Weekly → Monthly → **Save Changes**.
  - Daily: expand **Enter hourly values** and fill the 24 inputs `daily occupancy at 00:00` … `23:00` with `form_input`. Weekly and Monthly take typed values too.
  - After saving, open the profile's Export tab and confirm the 24 factors match the agreed values exactly; report any mismatch.
  - Alternative for precise curves: **Import Meter Data** with a CSV, if the user has one.
- Never edit a shared library profile used by other projects without asking; duplicate it instead.
