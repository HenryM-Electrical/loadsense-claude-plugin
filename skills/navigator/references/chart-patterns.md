# Mini chart patterns

The user wants a small inline chart after every meaningful query or change. Use whichever inline chart tool the session offers (e.g. a native line/bar chart tool, or the Visualizer). If none is available, give a compact markdown table instead.

## General rules
- Title says what changed, e.g. "Block A heat pump profiles vs kitchen (Restaurant profile)".
- Subtitle/axis states the unit: "Fraction of design load by hour", "kW", "kVA".
- x-axis 00 to 23. LoadSense profile exports start at 05:00: **reorder to 00:00 first** before plotting.
- Max 4 to 5 series; name every series.
- Follow the chart with one or two sentences: the takeaway (peak shift, kVA headroom, which load drives the peak). No restating every value.
- Where colour is supported, LoadSense palette: Brand Green #3DD6A0 for the improved/after case, accent red #E85A5A for the problem/before case.

## Pattern A: profile overlay (line)
Series = 2 to 5 profiles, values = 24 hourly factors 0 to 1. Use to compare shapes, spot coincident peaks.

## Pattern B: before/after demand (line)
Series "Before" and "After", values = hourly site or section demand (kW or kVA). Source: Battery Debug "Demand"/"Adjusted" columns, or reconstruct from profile × design kW when no hourly table exists (say so).

## Pattern C: KPI delta (bar)
Categories: Gross, Diversified, Coincident (and per-section kVA if relevant). Series "Before" and "After". Pair with a one-line delta, e.g. "Coincident 1,212 → 1,168 kW (-44 kW, -3.6%)".

## Pattern D: section loading vs capacity (bar)
Categories = sections (Existing TX, New TX). Series = peak kVA, plus capacity as a second series. Call out any section over capacity and by how much.

## Pattern E: time-band kVA (bar)
From Grid Capacity Negotiator bands, one bar per band, per section.
