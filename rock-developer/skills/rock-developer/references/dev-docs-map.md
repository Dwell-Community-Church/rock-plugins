# Rock RMS Developer Documentation Map

Routing index for Rock's official DEVELOPER documentation. Resolve a dev question to the right
URL, then fetch it live for current content. Base host: `https://community.rockrms.com`.

## Routing rules (read before fetching)

**Version note:** Rock's developer hub has no version selector, so treat dev docs as "latest" and
apply the version caveat in `SKILL.md` (most churches run a version or two behind current). For
versioned community pages that accept it (Lava, church-management docs) you may append a version
query for the current published version, e.g. `?Version=v19.0`; the `/developer` course and book
pages track current.

1. **Prefer the official developer docs** under `/developer/...`, plus `/Lava` and `/api-docs`.
2. **Fetch leaf/chapter articles, not bare book/section hubs.** A book hub (e.g.
   `/developer/obsidian`) is a navigation shell; the real content lives in its chapters. If you
   don't know the chapter slug, fetch the hub ONCE to harvest its child links, then fetch the
   chapter. Chapters can be layered, so it's fine to also read a foundational sibling the target
   builds on (e.g. `creating-blocks` before `creating-list-blocks`). What to avoid is blind URL
   guessing, not deliberate follow-through to pages the docs point you to.
3. **Lava is at `/Lava`** (`/lava/filters`, `/lava/tags`, `/lava/commands`, `/lava/shortcodes`).
4. **REST / Web API is at `/api-docs`** (not under `/developer`).
5. If a fetch fails or the runtime can't fetch, give the user the direct URL from this map plus
   your best grounded answer, and say it's worth confirming on the page.
6. **Follow doc-to-code pointers.** Some dev chapters are thin and defer to a reference
   implementation in the Rock source (e.g. the `ObsidianGalleryList` block on GitHub). When a
   page points you to a repo example for the real code, follow that pointer.

## Map: Rock developer docs

Everything here is DEV. `Area` tags the kind of work.

### Learning courses (fundamentals)

The numbered courses (101/202/303) are course landing pages that do **not** return article content to a
direct fetch (they behave like section hubs — a raw fetch of `/developer/101` returns "Article was not
found"). `quickstart-tutorials` does return content. To pull real lesson content, open the `/developer`
hub and harvest the lesson link, or fetch `quickstart-tutorials`.

| Topic | URL | Area |
|---|---|---|
| Quickstart tutorials | `/developer/quickstart-tutorials` (directly fetchable) | onboarding |
| 101 Launchpad | landing page; open `/developer` and harvest the lesson slug | C# / plugin basics |
| 202 Ignition | landing page; open `/developer` and harvest the lesson slug | entities, data model, blocks |
| 303 Blast Off (advanced) | landing page; open `/developer` and harvest the lesson slug | jobs, workflow actions, REST, RealTime, data-view filters |

### Lava (templating), root `/Lava`

The Lava category pages are **hubs, not leaf content** — the actual syntax lives one level
deeper. Fetch the child page; if you don't know its slug, fetch the hub once to harvest it.
Confirmed pattern: filters are `/lava/filters/<category>-filters`, tags are `/lava/tags/<name>-tags`.

| Topic | Hub | Go deeper to (examples) | Area |
|---|---|---|---|
| Lava home | `/Lava` | (overview only) | overview |
| Filters | `/lava/filters` | `/lava/filters/person-filters` (confirmed), `.../date-filters`, `.../string-filters` | filters |
| Tags | `/lava/tags` | `/lava/tags/for-tags` (confirmed), `.../if-tags` | control-flow tags |
| Commands | `/lava/commands` | `/lava/commands/entity`, `/lava/commands/sql` | data commands |
| Shortcodes | `/lava/shortcodes` | harvest a child from the hub | reusable snippets |

### Front-end frameworks
| Topic | URL | Area |
|---|---|---|
| Obsidian (Vue 3 blocks) | `/developer/obsidian` | block dev, field types, grids, UI controls (fetch a chapter) |
| Helix (HTMX + Lava pages) | `/developer/helix` | HTMX-in-Rock, forms / controls, endpoints (fetch a chapter) |

> HTMX in Rock is documented inside the Helix book. For the HTMX library itself, use `https://htmx.org`
> (external to Rock): `/docs` for the guide, `/reference` for the full attribute and API list (`hx-get`,
> `hx-post`, `hx-swap`, `hx-target`, `hx-trigger`, and the rest), and `/examples` for common patterns
> (active search, click-to-edit, infinite scroll). Fetch these live; there's no need to vendor them.

### Backend, architecture, standards
| Topic | URL | Area |
|---|---|---|
| Developer Codex | `/developer/developer-codex` | coding standards, naming, service layers, migrations, testing (fetch a chapter) |
| SQL Style Guide | `/developer/sql-style-guide` | Rock SQL conventions |
| Packaging plugins & themes | `/developer/packaging-plugins-themes` | distributing custom code |
| AI Agents | `/developer/ai-agents` | Rock's AI agent features |
| REST / Web API | `/api-docs` | API reference |

> The entity / data-model reference isn't a standalone page; it's covered within **202 Ignition**
> and the **Obsidian** and **Codex** books. Route there and drill into the relevant chapter.

## When a topic isn't listed

Reason from the sections above and fetch a plausible `/developer/...` chapter. If you land on a
book hub shell, harvest its child links and fetch the right chapter. Last resort: point the user
to `https://community.rockrms.com/Developer`.

## Curated references note

Some hard-to-find dev knowledge lives in the skill's vendored references, not in this map: PersonAlias
resolution (`references/person-alias-resolution.md`) and hand-authoring importable workflow JSON
(`references/workflow-export-import.md`). This map covers Rock's official online docs. For local
conventions not in either place, answer with general Rock behavior and note your own setup may differ.
