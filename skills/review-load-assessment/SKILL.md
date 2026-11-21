---
name: review-load-assessment
description: "Carries out a thorough engineering review of a LoadSense load assessment in the current scenario: every section, group and load, occupancy profiles, solar PV and battery storage, plus a hunt for missing loads. Produces a findings report with suggested load types, better-fitting profiles or new profiles. Use when the user says \"review this load assessment\", \"check my loads\", \"audit the project\", \"what have I missed\", \"sense-check the assessment\", \"QA this before issue\", or asks whether the profiles or load types are right."
---

# Review a load assessment

Load `navigator` first. This skill is **read-only** until the user approves changes. Never edit during the review pass; record findings, then offer fixes.

## 0. Frame the review
Record and state at the top of the report:
- Project name, category, description, active **scenario** and **revision**.
- Building type(s) and use (from the project category, description, group names and load names). If the use is genuinely unclear, ask one question before the missing-loads check (e.g. "Is the restaurant open to non-residents?"); otherwise state the assumption.
- Results snapshot: Gross, Diversified, Coincident (kW and kVA), peak hour, per-section peak kVA, suggested substation and utilisation (Grid Capacity Negotiator), Portfolio Benchmark text from Insights.

## 1. Inventory (build the table first)
Collapse all, then walk the tree top down. For each load, expand it, `read_page` the form, then collapse it again. Build one table:

| Section | Group | Group CF | Load | Type | Key inputs | kW | kVA | PF | Profile | Enabled |
|---|---|---|---|---|---|---|---|---|---|---|

Record the profile ID for each distinct profile (combobox option link `href=/profile/{id}`, then Escape). Note disabled sections, groups and loads separately: they are excluded from results and are a common source of error.

For each distinct profile, read the hourly factors from `/profile/{id}` → **Export**, plus weekday and monthly factors from the Edit Profile wizard (Next through, read, **Cancel**). Record category and source (Manual or Meter).

## 2. Check each level
Work through `references/review-checklist.md`. Typical checks:

**Sections (supplies / transformers)**
- Each section maps to a real supply; names match the drawings.
- Section peak kVA vs suggested substation and utilisation; flag utilisation that leaves little headroom or a gross over-size.
- Spare capacity and derating set deliberately, not left at defaults without comment.
- Phase setting appropriate.

**Groups**
- Group Coincidence Factor is part of the Engineered Diversity Method: time-based profiles show how each load is used through the day; the CF reflects the chance of everything peaking at exactly the same moment across a project of scale. Do NOT flag a CF below 1.0 on time-based profiles as double counting.
- Typical CF 0.8 to 0.9. Only raise a CF as a low-severity "Confirm" if it sits well outside that range without an obvious reason, or if a group with many apartments is left at 1.0 (a lower CF is usually justified at scale).
- Groups mirror real buildings, floors or tenancies; no loads parked in the wrong group or section.

**Loads**
- Right load **type** for the plant (area-based vs apartment vs discrete vs heating/cooling vs EV). Examples: apartments modelled as room/area loads instead of the Apartment type; a heat pump entered as a discrete kW without its thermal/COP basis; a kitchen entered as W/m².
- Inputs plausible: areas consistent with the schedule, quantities, ratings, power factor (flag every 1.0 or default 0.95 on motor-heavy plant).
- Power density: judge it in context. Before calling a W/m² low, establish where heating, DHW and cooling are carried. Apartment small power and lighting of 10 to 15 W/m² is normal for Passivhaus-standard dwellings when heating sits in a communal ASHP or elsewhere; the old 3 kW-per-apartment rule of thumb is itself on the high side. Only flag low density if the space is meant to be electrically heated locally and no other load carries that heat.
- Equipment parameters (COP, efficiencies, ratings, charger kW): if a value is plausible, do not propose a different number. Raise it as "Confirm" (e.g. "Winter COP 3.0 assumed: confirm against the selected unit") and only suggest an alternative when the value is clearly outside a credible range.
- Duplicates (two loads with the same name or identical inputs), placeholders ("Load 1", round numbers that look unconfirmed), and zero or near-zero loads.
- Each load has a profile. "No profile" = flat full load; flag unless intentional.

**Profiles**
- Profile matches the load's real operating pattern (heating peaks in cold mornings and evenings, cooling in summer afternoons, catering around service, EV per charging strategy, irrigation and plant per schedule).
- Seasonality: heating and cooling profiles must vary by month; all-1.00 monthly factors are a finding.
- Weekday pattern matches the use (offices and schools quieter at weekends; hospitality busier).
- Shape quality: binary 0/1 step profiles, flat 24 h placeholders, and peaks at implausible hours.
- One generic profile reused across very different loads.
- Meter-derived profiles preferred where they exist for a similar building; check the library.

**Solar PV**
- Scope (whole site, section or group) matches where the array is physically connected.
- Size, orientation, tilt, shading and performance ratio plausible; location region right for the site.
- Annual yield (kWh/kWp) sensible for the region; peak generation hour vs the demand peak hour (PV rarely helps an evening peak).

**EV charging**
- Load management mode, Static Bank Limit, import headroom and the **Smart override** state (EV Load Management panel in Presentation mode, and the EV load's settings).
- Compare Peak EV Demand with the Static Bank Limit. If EV demand is held at the static limit while the site has import headroom, flag that EV may be under-represented and suggest enabling Smart override (EV can exceed the static limit, but only within the import limit) and/or reviewing the import limit against the site peak.
- Note where the EV profile sits at its peak but load management dispatches near 0 kW at that hour: say what that relies on.

**Battery storage**
- Scope matches where the battery is connected; a battery on one section cannot relieve another.
- Charge mode fits the intent: Solar Only needs PV in the same scope, otherwise it never charges.
- Time Set Points: discharge windows cover the scoped section's peak hour; charge windows do not create a new peak (grid charging adds load).
- Capacity vs power vs window length: can it sustain the discharge for the whole window? Daily cycles and peak shaving from Battery Summary.
- Coincident demand quoted after battery: confirm the figure used for sizing and flag if Presentation and Analysis disagree.

## 3. Missing loads hunt
Using the building type, compare the inventory against the typical load list in `references/review-checklist.md`. Also check:
- Loads mentioned in group names, descriptions or Insights but absent from the tree.
- Life-safety and landlord loads (fire alarm, sprinkler and booster pumps, smoke extract, lifts, emergency lighting, BMS, security, comms).
- External loads (external lighting, car park, gates, signage, irrigation).
- Future-proofing allowances (EV passive provision, heat pump conversion, spare ways) where the brief implies them.
List each as "Possibly missing" with the reason; never assume it is missing for certain.

## 4. Report
Lead with the verdict in one line, e.g. "12 findings: 3 high, 5 medium, 4 low. Coincident demand likely understated by the missing kitchen extract and lift loads."

Then:
1. **Findings table**, highest severity first:

| # | Severity | Level | Item | Finding | Suggested action | Likely effect on peak |
|---|---|---|---|---|---|---|

Severity: **High** = could change plant or supply sizing, or a safety load missing. **Medium** = wrong profile or type that shifts the peak hour or magnitude. **Low** = tidy-up, naming, documentation. **Confirm** = a plausible assumption the engineer should verify (COP, ratings, CF choice); state the value used and what to confirm it against, without proposing a replacement.
"Likely effect" is directional (up, down, shifts later) unless estimated from design kW × profile factor at the peak hour; say which.

2. **Profile suggestions**: for each, the load, current profile, proposed library profile (by name and ID) or "new profile needed". For a new profile, propose 24 hourly factors plus weekday and monthly factors and draw them against the current profile (chart Pattern A).
3. **Possibly missing loads**: grouped by section/group, with a suggested load type and the profile to start from.
4. **Charts**: at minimum, a profile overlay of the loads that coincide at the site peak hour, and each section's peak kVA against its suggested substation (Pattern D). See `navigator/references/chart-patterns.md`.
5. **Assumptions and open questions** for the engineer.

Close with options, not a single instruction: apply all high-severity fixes in a duplicated scenario; apply selected items; draft the new profiles; or export the findings as a review note.

## 5. Applying fixes (only after approval)
- Duplicate the scenario first (`Duplicate scenario: {name}`) unless the user names one to change.
- Apply one finding at a time through `change-impact`, so each has its own before and after.
- New profiles through `interrogate-profiles` (Daily → Enter hourly values, Weekly, Monthly, then verify on Export).
- Missing loads through `build-load-assessment`.
- Finish with the running change log and the revised headline results.

## Guardrails
- Read-only until approved. Never delete; suggest disabling instead.
- Do not present benchmarks from memory as fact. Use LoadSense's Portfolio Benchmark, the user's own figures, or label a value as "typical, to be confirmed".
- Defer to the Engineered Diversity Method and the engineer's stated design basis. Challenge only where the evidence in the project contradicts itself.
- Before reporting two figures as inconsistent, check whether they measure different things (see "Metrics that differ by design" in `navigator/references/ui-map.md`).
- Results remain indicative and must be checked by a competent engineer.
