# Review checklist and typical load lists

Use these lists to prompt questions, not as proof that something is missing. Every "possibly missing" item needs a reason tied to this project.

## Red flags at a glance
- Load with "No profile".
- Power factor exactly 1.0 on motors, pumps, fans or heat pumps; or every load left at 0.95.
- A large apartment group left at Group CF 1.0 (a lower CF is usually justified at scale), or a CF well outside 0.8 to 0.9 with no stated reason. (A CF below 1.0 on time-based profiles is normal practice, not double counting.)
- Apartments modelled as room/area loads rather than the Apartment type.
- EV held at its Static Bank Limit with Smart override off while the site has import headroom.
- Heating or cooling profile with flat monthly factors (all 1.00).
- Binary 0/1 or flat 24 h profiles on loads that clearly vary.
- The same profile on loads with different operating patterns.
- A disabled load, group or section that the user may not realise is excluded.
- Solar Only battery with no PV in its scope.
- Battery discharge windows that miss the scoped section's peak hour.
- PV or battery scoped to the whole site on a multi-section site.
- Duplicate loads, "Load 1" style names, suspiciously round inputs.
- Site peak hour very different from the Portfolio Benchmark average, with no explanation.

## Typical loads by building type (prompts for the missing-loads hunt)

**Residential (houses and apartments)**
Apartment small power and lighting, cooking, heat pumps or direct electric heating, domestic hot water (cylinder immersion, heat pump DHW), MVHR, EV charging (passive and active), communal lighting, lifts, door entry and access control, fire alarm and smoke extract, sprinkler pumps, landlord plant room, bin store and bike store, external lighting, comms room.

**Offices and commercial**
Small power and lighting (W/m²), HVAC (AHUs, VRF/heat pumps, FCUs, chillers), DHW, lifts, server or comms rooms with cooling, kitchens and tea points, EV charging, external and car park lighting, BMS, security, fire alarm, sprinkler and booster pumps, tenant fit-out allowance.

**Hospitality (hotels and restaurants)**
Guest rooms (small power, lighting, room HVAC), commercial kitchen (cooking, refrigeration, warewashing, extract fans), bars and coldrooms, laundry, DHW (often a large and peaky load), pool and spa plant, lifts, back-of-house, events and AV, EV charging, external and landscape lighting, irrigation, gardens and glasshouses.

**Education (schools and colleges)**
Classrooms, ICT suites and server room, science labs and fume cupboards, DT workshops (three-phase machinery), kitchen and servery, sports hall and changing (DHW peak), heat pumps, ventilation, lifts, external sports lighting, EV, out-of-hours lettings.

**Healthcare**
Clinical small power, imaging (MRI, CT) as discrete plant, theatres and HVAC, medical gases and vacuum plant, sterile services, pharmacy cold chain, kitchens, laundry, lifts, essential supplies and UPS, generators (if modelled), EV.

**Industrial and logistics**
Process machinery, compressors, refrigeration and cold stores, dock levellers and doors, forklift and HGV charging, warehouse lighting, offices, welfare, external yard lighting, sprinkler pumps.

**Mixed use and estates**
All of the above by tenancy, plus landlord and estate loads: external lighting, gatehouses, pumping stations, irrigation, events power, EV hubs.

## Profile fit prompts
- Heating: winter-weighted monthly factors; morning and evening peaks for residential and hospitality; occupied hours for offices and schools.
- Cooling: summer-weighted; afternoon peak.
- Kitchens: breakfast, lunch and dinner service shape; prep before service; extract follows cooking.
- DHW: morning and evening peaks (residential, hotels), post-sport peaks (schools).
- EV: managed or unmanaged; workplace daytime vs residential evening and overnight.
- Pumps and ventilation: often near-continuous; check against controls strategy.
- Life-safety plant: normally negligible running load but may matter for standby and test; follow the project's approach.

## Solar and battery prompts
- PV scope = the board it is wired to.
- Specific yield sensible for the region; orientation and tilt match the roof.
- PV peak hour vs demand peak hour.
- Battery: scope, charge mode, windows aligned to the scoped peak, capacity long enough for the discharge window, grid charging not creating a new peak.
