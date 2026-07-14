# Rock RMS Official Documentation Map

This is a routing index for Rock's official documentation. Use it to resolve a
question to the correct documentation URL, then fetch that page for the current,
authoritative content. Base host: `https://community.rockrms.com`.

## Routing rules (read before fetching)

**Version pin (important):** Rock only publishes docs for the current version, and most churches run a
version or two behind, so older deep links return "Article was not found." Append **`?Version=vXX.0`**
(the current published version, for example `?Version=v19.0` today) to every documentation URL you
fetch, and apply the version caveats from the "Rock version" section of `SKILL.md` (flag
version-sensitive answers; be cautious with newer/advanced features that may not be in the reader's
version — verify rather than assert).

1. **Prefer the current docs** under `/documentation/...`. They are maintained and
   authoritative for "how do I use Rock today" questions.
2. **Fetch leaf/chapter/article URLs, not bare section indexes.** A one-segment
   section index (e.g. `/documentation/church-management/people`) is a navigation
   shell and often returns the literal body "Error." Go one or two levels deeper to
   a real article (e.g. `.../people/overview/intro-to-people`).
   **If you don't know the exact leaf slug, don't guess repeatedly.** Fetch the section
   index page ONCE — its body is a shell, but its HTML still lists the real child-article
   links. Harvest the correct leaf slug from that list, then fetch the leaf. One shell
   fetch to discover, one leaf fetch to answer — never a string of guessed URLs.
3. **Lava lives at `/Lava`** (and `/lava/filters`, `/lava/tags`, `/lava/commands`,
   `/lava/shortcodes`) — NOT under `/documentation`.
4. **Developer docs live at `/developer`** (e.g. `/developer/101`) — `/documentation/developer` 404s.
5. **Legacy docs (`/legacydocs`, numeric `bookcontent/{id}/{id}` URLs) are a last
   resort only.** They self-declare as superseded ("our documentation has moved").
   Don't construct legacy URLs from memory — their IDs are opaque. Use current docs.
6. If a fetch fails or the runtime can't fetch, **give the user the direct URL** from
   this map so they can read it, plus your best general answer.

## Map: current docs (`/documentation/...`)

Audience tags — **EU** = everyday staff/end-user, **ADM** = admin/configurer, **DEV** = developer.

### Getting started / fundamentals
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Rock Fundamentals | `/documentation/core-concepts/rock-fundamentals` | Entities, attributes, defined types, blocks, jobs, campuses; orientation | EU/ADM |
| Navigate Rock | `/documentation/core-concepts/rock-fundamentals/navigate-rock` | UI orientation for new users | EU |
| Entities / Attributes | `/documentation/core-concepts/rock-fundamentals/entities` · `.../attributes` | Core data model concepts | ADM/DEV |
| Security | `/documentation/core-concepts/security` | Roles, auth, securing entities | ADM |
| Search | `/documentation/core-concepts/search` | Universal + smart search | EU |

### People & families
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Intro to People | `/documentation/church-management/people/overview/intro-to-people` | How Rock stores people, families, addresses, age/grade | EU |
| People Basics | `/documentation/church-management/people/people-basics/add-a-person` (also edit/delete) | Everyday person-record tasks | EU |
| Person Profile Page | `/documentation/church-management/people/person-profile-page` (fetch a child article) | Profile tabs, badges, tags | EU |
| Families | `/documentation/church-management/people/families` (fetch a child article) | Add/edit, pre-register, blended families | EU |
| Person Attributes | `/documentation/church-management/people/person-attributes` (fetch a child) | Custom attribute setup & display | ADM |

### Check-in
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Intro to Check-in | `/documentation/church-management/check-in/check-in-fundamentals/intro-to-check-in` | Models, family vs individual, terminology | EU/ADM |
| Registration at the kiosk | `/documentation/church-management/check-in/registration/intro-to-registration` (also `.../registration/edit-families`) | Add/edit a family or person at the kiosk when a name doesn't come up (Registration Mode) | EU |
| Prepare / Plan | `/documentation/church-management/check-in/prepare-for-check-in` (fetch a child article) | Admin screen, sample configs, planning | ADM |
| Configure Check-in | `/documentation/church-management/check-in/configure-check-in` (fetch a child article) | Service times, areas/groups, ability levels, schedule builder | ADM |
| Kiosks / Labels / Manager | `/documentation/church-management/check-in` (fetch a child article) | Device, label, operational setup, Check-in Manager | ADM |

### Groups & attendance
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Groups (hub) | `/documentation/engagement/groups` (fetch a child article) | Groups overview, 15 chapters | EU/ADM |
| Manage Groups / Group Types | `/documentation/engagement/groups/manage-groups` · `.../group-types` | Create and structure groups | ADM |
| Group Members / Attendance | `/documentation/engagement/groups/group-members` · `.../group-attendance` | Membership & attendance | EU |
| Group Scheduler / Schedules | `/documentation/engagement/groups/group-scheduler-page` · `.../group-schedules` | Volunteer scheduling | ADM |
| Group Finder / Leader Toolbox | `/documentation/engagement/groups/group-finder` · `.../group-leader-toolbox` | Public finder + leader tools | EU |

> Attendance has no standalone top-level section: use **Group Attendance**
> (`/documentation/engagement/groups/group-attendance`) and check-in-driven attendance
> under the Check-in section.

### Reporting / data views
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Data Views | `/documentation/church-management/reporting/data-views/add-a-data-view` (also `.../use-filter-groups`, `.../secure-data-views`) | Build/filter/secure dynamic lists | EU/ADM |
| Reports | `/documentation/church-management/reporting/reports/create-a-report` (also `.../intro-to-reports`, `.../persist-data-views-in-reports`) | Tabular reports, Lava in reports, security | EU/ADM |
| Metrics | `/documentation/church-management/reporting/metrics` (fetch a child article) | KPI/metric tracking | ADM |
| Power BI | `/documentation/church-management/reporting/power-bi` (fetch a child article) | External BI integration | ADM/DEV |

### Communications
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Prepare for Communications | `/documentation/engagement/communications/prepare-for-communications` | Lists, templates, transports/mediums | ADM |
| Email / SMS / Push | `/documentation/engagement/communications/email` (· `/sms` · `/push-notifications`) | Per-channel setup & sending | ADM |
| Send a Communication | `/documentation/engagement/communications/send-a-communication` | Wizard, simple editor, comm flows | EU/ADM |
| Communication Reports | `/documentation/engagement/communications/communication-reports` | Analytics, saturation, unsubscribe | ADM |

### Giving / finance
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Financial Components / Track Giving | `/documentation/church-management/finance/financial-components` · `.../track-giving` | Accounts, batches, recording contributions | ADM |
| Online / Text Giving | `/documentation/church-management/finance/online-giving` · `.../text-giving` | Digital giving setup | ADM |
| Payment Gateways | `/documentation/church-management/finance/payment-gateways` | Gateway configuration | ADM/DEV |
| Contribution Statements / Finance Reports | `/documentation/church-management/finance/contribution-statements` · `.../finance-reports` | Year-end statements & financial reporting | ADM |

### Workflows
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Workflow Components / Build a Workflow | `/documentation/core-concepts/workflows/workflow-components` · `.../build-a-workflow` | Concepts + step-by-step authoring | ADM |
| Workflow Actions | `/documentation/core-concepts/workflows/workflow-actions` | Action reference | ADM/DEV |
| Entry Forms / Form Builder | `/documentation/core-concepts/workflows/entry-forms` · `.../form-builder` | User-facing workflow forms | ADM |

### Lava (templating) — root is `/Lava`, not `/documentation`
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Lava home | `/Lava` | Liquid-based templating: output vs tags | ADM/DEV |
| Filters | `/lava/filters` | Text, date, numeric, array, person, attribute filters | DEV |
| Tags | `/lava/tags` | if/for/case/cycle/include, etc. | DEV |
| Commands | `/lava/commands` | entity, SQL, cache, workflow, web-request | DEV |
| Shortcodes / API | `/lava/shortcodes` · `/lava/lava-api` | Advanced authoring | DEV |

### Developer — root is `/developer`, not `/documentation`
| Topic | URL | Covers | Aud |
|---|---|---|---|
| Developer hub | `/developer` | Learning paths + resource index | DEV |
| 101 / 202 / 303 | `/developer/101` · `/developer/202` · `/developer/303` | Progressive dev curriculum (entities, data models, migrations) | DEV |
| REST API / SQL style / plugin packaging | linked from `/developer` | API reference, query standards, distributing code | DEV |

## When a topic isn't in this map

Rock is large; this map covers the common areas. For anything else, the current-docs
URL pattern is `/documentation/{category}/{section}/{chapter}/{article}` with readable
slugs — reason from the five categories (core-concepts, church-management, engagement,
digital-publishing, supporting-rock), fetch a plausible article URL, and if you hit a
navigation shell, adjust to a child article. As a last resort, tell the user the closest
section and link them to `https://community.rockrms.com/documentation`.
