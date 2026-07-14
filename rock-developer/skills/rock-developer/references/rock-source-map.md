# Rock RMS Source Code Map

Rock is open source. When the official docs don't give the **exact** implementation detail a
question needs (an entity's real properties, a table's columns, an enum's values, a Lava filter's
behavior, a migration's specifics), fetch the authoritative source directly from GitHub. This is a
**precision fallback, not the first move**: answer from the docs (fast), and reach into the source
only when exactness matters.

All Rock paths below were verified on the **`develop`** branch of `SparkDevNetwork/Rock`.
Raw-file base: `https://raw.githubusercontent.com/SparkDevNetwork/Rock/develop/<path>`

## How to fetch
- **Known file:** fetch the raw URL directly.
- **Unknown exact filename:** list the folder via the public contents API,
  `https://api.github.com/repos/SparkDevNetwork/Rock/contents/<folder>?ref=develop`, find the file,
  then fetch its raw URL. Do NOT rely on GitHub code search; it needs an auth token the runtime
  won't have.
- **URL-encode spaces as `%20`** (matters for the migrations path).

## Rock source map (`SparkDevNetwork/Rock`, branch `develop`)

| Area | Path | Example | Notes |
|---|---|---|---|
| Entity / data models | `Rock/Model/<Domain>/<Entity>/` | `Rock/Model/CRM/Person/Person.cs` | Each entity is its OWN folder with `<Entity>.cs`, `.Logic.cs`, `.SaveHook.cs`, `<Entity>Service.cs`. Domains: CRM, Finance, Group, Communication, Event, Engagement, Core, Connection, Workflow, LMS, Prayer, Reporting, Security, Meta, WebFarm, AI. e.g. `Rock/Model/Group/GroupMember/`, `Rock/Model/Finance/FinancialTransaction/`. |
| Enums | `Rock.Enums/<Domain>/` | `Rock.Enums/Crm/...` | Standalone project. Some legacy enums are inline in model files; modern canonical home is here. |
| SystemGuid constants | `Rock/SystemGuid/` | `Rock/SystemGuid/Person.cs` | Flat, one file per class (Group, DefinedType, DefinedValue, Attribute, BlockType, Page, FieldType, EntityType, ...). |
| Lava filters (Rock impl) | `Rock/Lava/Filters/` | `Rock/Lava/Filters/LavaFilters.Person.cs` | Filter methods split by domain: `.Text.cs`, `.Person.cs`, `.Reporting.cs`, `.Identifiers.cs`, base `LavaFilters.cs`. |
| Lava commands/blocks | `Rock/Lava/Blocks/` | `Rock/Lava/Blocks/SqlBlock.cs` | `{% sql %}`, `{% cache %}`, `{% execute %}`, `{% search %}`, `{% webrequest %}`, etc. Shortcodes in `Rock/Lava/Shortcodes/`. |
| Lava engine (core) | `Rock.Lava/` | `Rock.Lava/Engine/LavaEngineBase.cs` | Engine abstraction. Fluid impl in `Rock.Lava.Fluid/`; contracts in `Rock.Lava.Shared/`. |
| EF migrations | `Rock.Migrations/Migrations/` (+ nested `Version XX.0/Version XX.0/`) | `Rock.Migrations/Migrations/Version%2019.0/Version%2019.0/<file>.cs` | The current in-flight rollup sits directly in `Rock.Migrations/Migrations/`; older versioned migrations are in the doubly-nested `Version XX.0/Version XX.0/` folder. URL-encode spaces (`%20`). Each = `.cs` + `.Designer.cs` + `.resx`. |
| Obsidian block, C# server | `Rock.Blocks/<Domain>/` | `Rock.Blocks/Crm/PersonDetail.cs` | Base types in root: `RockEntityDetailBlockType.cs`, `RockObsidianBlockType.cs`. |
| Obsidian block, Vue `.obs`/TS | `Rock.JavaScript.Obsidian.Blocks/src/<Domain>/` | `.../src/Crm/personDirectory.obs` | Main block = camelCase `.obs` at domain root. Complex blocks add a PascalCase subfolder with child `.obs` + `types.partial.ts`. |
| Obsidian shared controls | `Rock.JavaScript.Obsidian/Framework/` | `.../Framework/Controls/<Control>.obs` | Shared UI kit (`@Obsidian/Controls/...`), plus Core, Enums, FieldTypes, Utility, ViewModels, SystemGuids. |
| Legacy WebForms blocks | `RockWeb/Blocks/<Domain>/` | `RockWeb/Blocks/Crm/PersonMerge.ascx` | `.ascx` markup + `.ascx.cs` code-behind. WebForms uses plural `Groups` (vs `Group` in models). |
| ViewModels / bags | `Rock.ViewModels/Blocks/<Domain>/<Block>/` | `Rock.ViewModels/Blocks/Crm/PersonDetail/...Bag.cs` | Block bags/boxes under `Blocks/`; entity view models under `Entities/`; shared under `Utility/` and `Controls/`. Suffixes: `...Bag.cs`, `...Box.cs`, `...OptionsBag.cs`. |
| REST API controllers | v2: `Rock.Rest/v2/` · legacy: `Rock.Rest/Controllers/` | `Rock.Rest/v2/ControlsController.cs` | Legacy = `...Controller.Partial.cs` (hand-written) + generated bases in `Rock.Rest/Controllers/CodeGenerated/`. |
| Jobs | `Rock/Jobs/` | `Rock/Jobs/GroupSync.cs` | Flat, one file per Quartz job. |
| Workflow actions | `Rock/Workflow/Action/<Domain>/` | `Rock/Workflow/Action/People/AddPersonToFamily.cs` | Domains: People, Groups, Communications, Connections, Finance, CheckIn, Cms, Entity, Registrations, Utility, AI, ... Base `ActionComponent.cs` in `Rock/Workflow/`. |
| Plugin migration base | `Rock.Plugin/` | `Rock.Plugin/MigrationNumberAttribute.cs` | The `[MigrationNumber(seq, "X.Y.Z")]` attribute plugin/hotfix migrations use; plugin migrations inherit `Rock.Plugin.Migration`. Pair with the `.claude/skills/plugin-migration` guidance below. |
| Integration tests (usage examples) | `Rock.Tests.Integration/<Domain>/` | `Rock.Tests.Integration/Crm/` | Domain-organized (AI, CheckIn, Communication, Core, Crm, Engagement, Model, Reporting, Rest, Security, Web, Workflow). Fetch to see how a service or API is actually called when the docs don't show usage. |

### Gotchas
- **Entities are folders, not single files.** You need the domain (CRM/Group/Finance/...) to build
  the path. Persisted `[DataMember]` columns live in **`<Entity>.cs`** (fetch this for the field
  list); `.Logic.cs`/`.SaveHook.cs` are computed/behavior and `<Entity>Service.cs` is data access.
  Every entity also inherits standard columns from `Model<T>` that do NOT appear in the file:
  `Id, Guid, ForeignId, ForeignGuid, ForeignKey, CreatedDateTime, ModifiedDateTime,
  CreatedByPersonAliasId, ModifiedByPersonAliasId`. Include these in any "complete field list."
- **Obsidian casing:** `.obs` files are camelCase (`personDirectory.obs`); C# classes and subfolders
  are PascalCase (`PersonDetail.cs`). One block spans three projects (server `.cs`, client `.obs`, bags).
- **Migrations:** doubly-nested `Version XX.0/Version XX.0/`, URL-encode `%20`; the newest rollup is
  at the `Migrations/` root.
- **Domain-name inconsistency:** models use `Group`/`CRM`; WebForms uses `Groups`/`Crm`;
  ViewModels/Enums use `Crm`. Try case/plural variants if a path 404s.

## Rock's own AI-agent guidance (`.claude/` in the repo)

Rock ships agent guidance in `SparkDevNetwork/Rock/.claude/` (branch `develop`). It is neither in the
online docs nor obvious from the code, so it is a distinct, high-value source for "how Rock wants it
done." Fetch the raw files like any other source path. (Skip `commands/`, `hooks/`, and
`settings.json`; those are Rock's internal Claude Code wiring, not reference material.)

**`.claude/rules/`** (concise standing rules):
- `data-model.md`: `Model<T>` columns, FK cascade behavior, and the `UpdateBlockTypeByGuid` data-loss
  gotcha (it can delete entity-based block types; use `AddOrUpdateEntityBlockType` for Obsidian/Mobile).
- `code-conventions.md`: Options POCO pattern, SQL string escaping inside `$@"..."`, `[RockObsolete]` usage.
- `block-architecture.md`: the `AttributeKey` / `PageParameterKey` nested-const block pattern.
- `obsidian-conventions.md`: Obsidian (Vue 3) block conventions.
- `rock-domains.md`: Rock's domain taxonomy (CRM, Group, Finance, Communication, ...).

**`.claude/skills/`** (task playbooks, each a folder with its own `SKILL.md`):
- `sql`: idempotent inserts, Guid-based lookups, audit columns, `IsSystem` is per-entity.
- `migration`: EF migration process and validation.
- `plugin-migration`: plugin hotfix numbering, SQL escaping, idempotency guards.
- `entity-model`: scaffolding a new entity (class, config, SystemGuid).
- `convert-block`: WebForms `.ascx` to Obsidian conversion order.
- `css-cleanup`: `.obs` CSS priority order and anti-patterns.
- `bugfix`: root-cause, cross-layer, blast-radius discipline.
- `review-conversion`, `spec`, `docs`, `commit`, `remember`: review, spec-writing, docs, commit, and
  memory workflows.

When a question is about the *right way* to do something in Rock (a convention, a migration, a
conversion), check the matching rule or skill here first: it is Rock core's own guidance, so it beats
inferring from a single example.

## Rock-SQL-Library (`SparkDevNetwork/Rock-SQL-Library`, branch `master`)

Raw base: `https://raw.githubusercontent.com/SparkDevNetwork/Rock-SQL-Library/master/<path>`

A community cookbook of ready-to-run `.sql` scripts (not application code), in domain folders: Core,
CRM, CMS, Finance, Groups, Engagement, Event, Check-in, Communication, Interactions, Prayer,
Reporting, Workflows, System Maintenance, Misc. Example: `Core/Get Exception Hierarchy.sql`.
Filenames contain spaces (URL-encode). Use when a question wants a proven query pattern for a Rock
table; a dev drops the script into Rock's SQL command, a Lava `{% sql %}` block, or SSMS.

## rock-development-tools (`SparkDevNetwork/rock-development-tools`, branch `main`)

Raw base: `https://raw.githubusercontent.com/SparkDevNetwork/rock-development-tools/main/<path>`

The tooling that scaffolds and builds Rock plugins / Obsidian blocks, under `src/`:
`SparkDevNetwork.Rock.DevTool` (the `rock` dev CLI), `.CodeGenerator` (generates the bags / TS
mirrors / REST bases that appear as `CodeGenerated` files in the main repo), `.Analyzers` (Roslyn
rules), `.Build.Tasks`, plus the Obsidian/JS build tools and `eslint-config-rock-recommended`.
Reference this to explain WHY generated code looks the way it does, or the codegen/analyzer/lint
rules; it's less useful for "fetch the implementation of feature X."
