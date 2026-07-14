---
name: rock-developer
description: >
  Rock RMS developer reference, answered from Rock's official developer docs and, when exact
  detail is needed, the open-source Rock code. Use for building or coding in Rock: Lava
  (filters, tags, commands, shortcodes), Rock SQL, C# blocks and plugins, Obsidian (Vue 3)
  blocks, the Helix framework and HTMX, the entity/data model, EF migrations, workflows and
  workflow actions, the REST/Web API, and plugin packaging. Trigger on "write a Lava", "Rock
  SQL", "create a Rock block", "Obsidian block", "Helix", "Rock plugin", "Rock migration",
  "Rock REST API", or any Rock development task. It routes to the right official developer doc
  page and fetches current content, so prefer it over answering dev questions from memory. For
  non-developer Rock usage (check-in, groups, reports), use the rock-documentation skill instead.
---

# Rock RMS Developer Reference

This skill answers Rock development questions from Rock's official developer documentation,
fetching the current pages rather than relying on memory. Rock's dev frameworks (Obsidian,
Helix, Lava) evolve quickly, so the live docs are the source of truth.

## Rock version: docs track the current release, your instance may be behind

Rock's developer hub has no version selector, so treat the dev docs as "latest." Most churches run a
version or two behind current, so keep the gap in mind:

- Most Lava, SQL, entity-model, and coding-standard material is stable across versions and applies
  as-is.
- Front-end frameworks move faster. Obsidian and Helix have changed across recent versions, so flag
  anything that looks new: "Rock's docs are ahead of your version, so verify this against your Rock." If
  a feature or API looks newer than the version you run, say so rather than assuming it's present.

## Route the question first

Decide where the answer lives before doing anything, and prefer local references (no fetch) over
fetching:

- **Persisted datasets, centralizing event config, or Lava page performance:**
  `references/rigging-pattern.md`. Local, no fetch.
- **Person data in SQL / C# / Lava** (grouping, counting, deduping, or "records for this person"):
  apply `references/person-alias-resolution.md`. Local, no fetch.
- **Hand-authoring importable workflow JSON:** `references/workflow-export-import.md`. Local, no fetch.
- **Official-doc topics** (Lava, Obsidian, Helix, HTMX, Codex, courses, REST API): resolve the URL in
  `references/dev-docs-map.md`, then fetch.
- **Exact implementation detail or Rock's own "right way" guidance** (an entity's real properties, a
  column, an enum value, a coding or migration convention): use `references/rock-source-map.md`, then
  fetch the file. Precision fallback, not the default.

When you do fetch, fetch the **minimum**: the one right leaf or chapter page, never a bare book hub and
never a speculative spread. Answer with working specifics (code, Lava, SQL, steps) and include the
link. If the runtime can't fetch, hand over the direct URL from the map plus your best grounded answer.

## Ground-truth from the Rock source (precision fallback)

Rock is open source, so when the docs don't give the exact detail a question needs (an entity's
real properties, a table's columns, an enum's values, a Lava filter's behavior, a migration's
specifics), fetch the authoritative source. Use this as a **fallback, not the default**: answer
from the docs first (fast), and reach into the code only when exactness matters, so the common
path stays quick.

`references/rock-source-map.md` has the verified GitHub paths and raw-URL patterns for the Rock
repo (branch `develop`) plus the Rock-SQL-Library and rock-development-tools repos. When you cite
source, link the file and remember the version caveat: the source tracks the latest Rock, so a class
or property may be newer than the version you run.

## Curated references (hard-to-find techniques)

Beyond the official docs and Rock source, these vendored references carry hard-to-find dev knowledge:

- `references/person-alias-resolution.md`: the PersonAlias identity rule (resolve `PersonAliasId` to
  `PersonId` before grouping, counting, deduping, or filtering "this person's records"). Apply it
  proactively on any person-data SQL, C#, or Lava. (Adapted from Greg Lawless's Rock documentation.)
- `references/workflow-export-import.md`: how to hand-author importable Share Workflow JSON (the
  `ExportedEntitiesContainer` schema, the `ReferenceType` enum, and entity-tree order). (Adapted from
  Greg Lawless's Rock documentation.)
- `references/rigging-pattern.md`: the Triumph "rigging pattern" for keeping large, multi-entity events
  clean and performant by centralizing lookups in one cached persisted dataset. Use for questions about
  persisted datasets, centralizing event config, or Lava page performance at scale. (From a Triumph
  Tech training.)

## Scope and honesty

- **Official Rock developer docs and source, plus a few curated references.** Most answers come from
  Rock's official docs and source. For conventions not covered here, answer with general Rock behavior
  and note your own setup may differ.
- **Don't fabricate APIs or signatures.** If the docs don't confirm a method, class, or Lava
  filter, say what the docs do cover and link them rather than inventing an interface.
- **Follow Rock's conventions** when you cite or write code: PersonAlias resolution when joining
  or counting people, Guid-based references, and the coding standards in the Developer Codex.
