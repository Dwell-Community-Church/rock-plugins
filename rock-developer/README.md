# rock-developer

Answers Rock RMS development questions by routing to Rock's official developer documentation and
fetching the current content.

## What's inside

- `skills/rock-developer/SKILL.md` — the dev-focused route-and-fetch workflow, with version-awareness.
- `skills/rock-developer/references/dev-docs-map.md` — an index of Rock's developer docs (courses,
  Lava, Obsidian, Helix, Developer Codex, API, packaging) plus routing rules.
- `skills/rock-developer/references/rock-source-map.md` — verified paths into the open-source Rock code
  (SparkDevNetwork/Rock) with raw-URL patterns, for fetching ground-truth from the source.
- Curated technique references: PersonAlias resolution, workflow export/import authoring, and the
  persisted-dataset "rigging" pattern.

## How it works

Rock's dev frameworks (Obsidian, Helix, Lava) change quickly, so this skill doesn't freeze a copy.
It maps a development question to the right official doc page and fetches it live, then answers with
code and the link.

## Scope

Official Rock developer docs and source, plus a few curated technique references. For everyday
non-developer Rock usage, use the general `rock-documentation` skill instead. This complements Triumph
Tech's `rock-techkit` (dev patterns, SQL, UI markup).

## Attribution

PersonAlias resolution and workflow export/import authoring are adapted from Greg Lawless's Rock
documentation work. The "rigging pattern" is from a Triumph Tech training.

## Requirements

Needs the runtime to fetch web pages (`community.rockrms.com`). Falls back to handing over the doc URL
if fetching isn't available.
