# rock-plugins

Claude skills and plugins for churches running **Rock RMS**, shared by the ICT team at
[Dwell Community Church](https://www.dwellcc.org).

These are Claude Code and Claude Cowork plugins we built for our own staff and cleaned up to share
with the Rock community. Use them, fork them, adapt them to your church. They're sanitized derivatives of what we run internally at Dwell, versioned independently of our internal builds.

> Looking for our general, non-Rock-specific tools (a PowerPoint builder, a daily brief, staff
> onboarding, and a brand-skill template)? Those live in
> [Dwell-Community-Church/dwell-plugins](https://github.com/Dwell-Community-Church/dwell-plugins).

## What's here

| Plugin | What it does |
|--------|--------------|
| [`rock-documentation`](./rock-documentation) | Answers Rock RMS questions by routing to and fetching Rock's official documentation, so answers stay current instead of guessed from memory. |
| [`rock-developer`](./rock-developer) | Rock RMS developer reference for Lava, SQL, Obsidian/Helix blocks, C# plugins, migrations, and the REST API, from the official developer docs and the open-source Rock code. |

## Install

In Claude Code:

```
/plugin marketplace add Dwell-Community-Church/rock-plugins
/plugin install rock-documentation@rock-plugins
/plugin install rock-developer@rock-plugins
```

## Looking for the writing and dev-pattern skills?

The excellent `essentials` (writing coach, write-like-me, style guide) and `rock-techkit` (Rock dev
patterns, SQL, UI markup) plugins are maintained by **Triumph Tech**, not us. Get them straight from
[github.com/Triumph-Tech/rock-claude-plugins](https://github.com/Triumph-Tech/rock-claude-plugins).

## Support

Shared as-is, with no warranty or guarantees (see [LICENSE](LICENSE)). Issues and pull requests are
welcome and we'll look when we can, but this is a side effort from a church ICT team, not a supported
product.

## Credits

Built by the Dwell Community Church ICT team. The Rock developer reference draws on community
documentation from Greg Lawless and a pattern shared by Triumph Tech. Thanks to the Rock community.

## License

[Apache License 2.0](LICENSE).
