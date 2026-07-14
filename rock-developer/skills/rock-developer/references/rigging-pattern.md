# Triumph rigging pattern (persisted datasets as centralized event "rigging")

Source: Triumph Tech training "Triumph Rigging Pattern" (Aug 2024). This is a Triumph *practice*, not
a Rock feature. Use it when answering questions about keeping large, multi-entity processes (big
registrations, retreats, recurring events) clean, centralized, and performant. For exact Lava syntax
of any feature named here (`PersistedDataset`, `ToJSON`, entity commands, the `{% lava %}` tag), fetch
`/lava` live rather than guessing.

## The problem it solves

A large ministry process (a retreat, a conference, a big registration) touches many entities: event
items and occurrences, a registration template and instance, groups of registrants and speakers,
content channels, locations and rooms, deadlines, and more. Pages that display or link to those either
hard-code the values (unmaintainable, and one date change means editing many pages) or look them up on
every page load (slow, and the same templates get repeated across many pages). Performance at scale is
the site builder's responsibility, not only the core team's; sloppy per-page lookups undo Rock's own
Lava optimizations.

## The core idea

Move all the lookups into ONE cached **persisted dataset** (the "rigging"), and have every page read
from that dataset instead of doing its own lookups:

- **One place to change config.** Update the event once and every page using the dataset updates.
- **Zero database calls at page load.** A persisted dataset is cached, so pages read it from memory.
  The expensive lookups (including slow attribute lookups) run only when the dataset refreshes on its
  schedule, never when a visitor loads the page.
- **The one tradeoff:** it is cached, not live. Data is only as fresh as the last scheduled refresh.

## Persisted dataset primer

A persisted dataset is a cached store of arbitrary data, built by a Lava "build script" and read back
in Lava via the `PersistedDataset` filter. Build the data with an entity command and shape it with the
`ToJSON` filter.

- **`ToJSON` pro tip:** run each item you output through `ToJSON`. It formats correctly (quotes text,
  leaves numbers and similar values unquoted). Do NOT run `ToJSON` on a bare entity to grab everything:
  it pulls in every nested property and can balloon a handful of records into a megabyte of server RAM.
  Pick the specific properties you need.
- **Reading it back:** load the dataset by its key with the `PersistedDataset` filter, then treat its
  contents like properties (dataset, then `event`, then `name`). Familiar Lava object access.

## Build script structure

1. **Configuration block at the top.** Put the entity IDs (event item, group, registration instance,
   content channels) and any static values (deadlines) at the top so config isn't buried in the
   template. Wrap the block in a `{% lava %}` tag so you don't have to wrap every line in `{% %}`;
   it's much cleaner to read.
2. **Per-entity sections.** For each entity, an entity command loads the properties you want, each run
   through `ToJSON`. Repeat for related entities (event item, occurrence, group, group members, parent
   group, and so on). You can mix and match properties from related entities however you like.
3. **Enable the Lava commands** the script uses (e.g. entity), and set the **refresh interval / cache
   duration** (e.g. 8 hours). Unlike the `{% cache %}` tag, a persisted dataset refreshes itself on
   schedule automatically, not lazily on the next request.

## Attribute inception (get all attributes without naming them)

Attributes are available in the dataset just like properties, and the (slow) attribute lookups happen
only at refresh time, not on page load. Rather than list every attribute key by hand, use the
"attribute inception" pattern (documented under `/lava`): a small template that loops an entity's
attributes and outputs each one's id, name, key, raw value, and formatted value, without knowing the
keys ahead of time. Give that loop a name (e.g. `event item attributes`) and drop it into the entity's
section of the build script. For a group it captures attributes from the group and the group type in
one loop. Once stored, attributes read back like any other property.

## Where staff pick the values (two config models)

You don't want staff typing entity IDs. Two ways to let them pick instead:

**Defined values (best for recurring events).** Create a defined type whose defined-value attributes
hold the config (event item, registration instance, group, content channel, plus dates such as
registration deadline, payment due, drop-off). Field-typed attributes give proper pickers, and the
attribute "show in grid" setting surfaces missing config at a glance. The build script reads the top
**active** defined value from the list and pulls its attributes, so going live with the next
occurrence is just marking the new value active and ordering it to the top. Bonus: you can pull the
entities directly from the value, skipping separate entity commands.

**Global attributes (best for one-off or more flexible setups).** Create a global-attribute category
(entity type = global attributes, i.e. no entity type), add the attributes to it, then have the build
script find all global attributes, filter to that category, and emit each one's data (id, key, name,
raw value, formatted value). To let staff self-serve, give them a page with an attributes block whose
settings restrict it to that category.

## Consuming it on pages

Every external page and staff control panel loads the dataset and reads properties from it (event
date, registration slug or URL, buttons linking to the event / registration / group / content
channel). Because it's centralized and cached, pages never need direct edits: when the dataset's
values change (for example flipping from a Fall to a Spring occurrence), every page updates
automatically and stays fast.
