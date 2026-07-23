# Dwell Component Library (example)

This is **Dwell Community Church's real web component library**, shared with the Rock community as a worked example. It shows a pattern any church can copy: a gallery of copy-paste UI components pulled from the live site, plus a machine-readable `CLAUDE.md` guide that Claude reads to build new pages and Rock HTML blocks that match your design.

It's an **example to learn from and adapt**, not a drop-in package. Copy the approach, then swap in your own components, colors, fonts, and content.

## What's here

- **`library.html`:** the gallery. Open it in a browser to preview every component, flip to its code, and copy it. Each one is real, in-production markup and CSS.
- **`CLAUDE.md`:** the machine-readable design guide: tokens, conventions, a component index, and composition order. This is what makes it work well with Claude.
- **`index.html`:** a redirect to the gallery.

## How to use it

**By hand:** open `library.html`, find a component, copy its code, and paste it into a Rock HTML block or page.

**With Claude (the useful part):** point Claude at `CLAUDE.md` and `library.html` and ask it to build. For example: *"Use the components, tokens, and conventions in this CLAUDE.md and library.html, then build me a hero and an events section."* Claude reads the guide, pulls the real components, and assembles an on-brand page. That is the pattern worth copying: a design guide written for an AI to read.

## Adapt it for your church

This is Dwell's library, so a few things are Dwell-specific. To make it yours:

- **Fonts:** Dwell uses Circe (a paid Adobe Fonts license) loaded site-wide from its theme, so it is not bundled here. Load your own fonts; the free Lato is left in as a preview stand-in.
- **Colors and tokens:** edit the palette and type scale in `CLAUDE.md` (and the component CSS) to your brand.
- **Content and links:** the header and footer contact details are placeholders (`555-555-5555`, an example address, `yourchurch` social links). Replace them with yours.
- **Images:** a couple of images point to Dwell's CDN (`res.cloudinary.com/dwellcc/...`). Swap in your own.
- **Theme asset paths:** paths like `/Themes/Dwell/Assets/...` are Dwell-theme-specific and won't resolve elsewhere. Point them at your own assets.

## Want a blank starting point instead?

If you'd rather build your own from scratch, there's a fill-in-the-blanks **`brand-example`** template in our general plugins repo: [Dwell-Community-Church/dwell-plugins](https://github.com/Dwell-Community-Church/dwell-plugins).

## Credits

Built by the Dwell Community Church ICT team (component library by Joey Ambrozich). Shared as-is under the repo's [Apache-2.0 license](../../LICENSE).
