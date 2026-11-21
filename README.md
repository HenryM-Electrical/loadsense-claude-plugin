<p align="center"><img src="logo.png" width="96" alt="LoadSense"></p>

# LoadSense for Claude

Lets Claude drive [LoadSense](https://loadsense.app), electrical loading and energy analysis software, through the Claude in Chrome extension. Claude builds load assessments, interrogates occupancy profiles, charts the impact of every change, and right-sizes transformers across scenarios, faster and with fewer misclicks.

## Install

In Claude Code (v2.1.275 or later):

```
/plugin install loadsense --marketplace HenryM-Electrical/loadsense-claude-plugin
```

Or in two steps:

```
claude plugin marketplace add HenryM-Electrical/loadsense-claude-plugin
claude plugin install loadsense@loadsense
```

In claude.ai, download `loadsense.plugin` from the latest [release](https://github.com/HenryM-Electrical/loadsense-claude-plugin/releases) and upload it.

## Requirements
- A LoadSense account at loadsense.app.
- The Claude in Chrome extension, signed in to LoadSense in the same browser.
- No API keys or connectors.

## Skills
| Skill | What it does |
|---|---|
| navigator | Core UI map, URL shortcuts, fastest text routes to data, safety rules |
| build-load-assessment | Brief → section/group/load plan → entered into LoadSense → Run Analysis → summary and chart |
| interrogate-profiles | Finds which profile each load uses, reads hourly, weekday and monthly factors, overlays and sanity-checks them |
| change-impact | Snapshots results before and after every edit and charts the delta |
| tx-scenarios | Transformer rebalancing loop, battery and solar levers, scenario hygiene and comparison |

## Safety
Claude asks before deleting anything, leaves the Baseline scenario alone unless named, never saves when only reading a profile, and reminds you that results are indicative and must be checked by a competent engineer.

## Links
- [FAQ](https://loadsense.app/faq) · [Contact and support](https://loadsense.app/contact)
- [Disclaimer](https://loadsense.app/disclaimer) · [Terms of Use](https://loadsense.app/terms) · [Privacy Policy](https://loadsense.app/privacy)

## Licence
MIT. See [LICENSE](LICENSE). The plugin is free; use of LoadSense itself is governed by the [Terms of Use](https://loadsense.app/terms).

## Maintenance
UI map captured 21 Nov 2026 (Loading, Solar, Battery, Presentation, scenarios, revisions, profile dashboard and editor, accessibility labels). Update `skills/navigator/references/ui-map.md` when the LoadSense UI changes.
