# Dwell Component Library

Reusable UI components for **dwellcc.org** (Rock RMS, Rock v17). Pull from here when building any new Dwell page or HTML block instead of designing components from scratch. Every component is the real, in-production markup + CSS pulled from the live site, with real responsive breakpoints.

- **Human gallery / source of truth:** `library.html` (paste-able into a Rock HTML block; renders a magicui-style gallery with Preview / Code / Copy per component).
- **This file:** the machine-readable index Claude should read first.

---

## How Claude should use this

1. Read this file to choose the right component(s) and learn the tokens + rules.
2. Get a component's real code from `library.html`: each component is a
   `<section class="cl-block" id="{ID}">` whose paste-able source lives inside its
   `<template class="cl-src">…</template>`. **Copy the template's inner HTML** (the `<style>` + markup, sometimes a `<script>`). Ignore everything with a `cl-` class — that is showcase chrome, not part of the component.
3. Assemble pages by stacking components in the order shown under **Composition** below.
4. Keep components **self-contained**: each ships its own `<style>`. Do not extract the CSS into a shared sheet unless asked — pasting the whole block is the intended workflow.

---

## Design tokens

**Fonts** — **Circe is the primary font for every component** (headings weight 900; body 400/700). Lato appears only as a fallback in the font stack and in the gallery's own chrome. Circe + Lato load site-wide from the Dwell theme; do not add `@font-face` or Google Fonts.

**Color palette**

| Token | Hex | Use |
|---|---|---|
| Dark teal | `#03272D` | Header, hero, quote surfaces; heading text |
| Deep green | `#034029` | Member notice strip |
| Filter dark | `#0d2e3a` | Resources filter panel, dark CTA pill |
| Leaf green | `#07841B` | Primary action, links, eyebrows, left borders |
| Leaf green (hover) | `#066916` | Button/link hover |
| Bright green | `#6DD400` | Filter accent line, rsrc icon tiles |
| Lime | `#72DB2B` | Header underline, "Give now" button, lime borders/tags |
| Section label | `#4a9c2d` | Grouped resource-list section labels |
| Body text | `#515E61` | Paragraph / description text |
| Heading text | `#03272D` | All headings |
| Card border | `#E0E5E0` | Standard card border (also `#e6ece9`, `#dfe6e2` in rsrc/cr) |
| Soft hover | `#F5F8F5` | Card hover background (also `#f6faf4` in rsrc) |
| Badge green | `#EAF3DE` bg / `#27500A` text | essay / teaching / podcast tags |
| Badge tan | `#FAEEDA` bg / `#633806` text | tool / class tags |
| Badge teal | `#E1F5EE` bg / `#085041` text | story tags |
| Banner gradient | `#E1F5EE` → `#EAF3DE`, border `#5DCAA5` | Featured-events banner |

**Type scale (approx, from components):** h1 38–40px · h2 26–28px · h3 18–24px · h4 16–17px · body 14–16px · small/meta 12–13px · eyebrow 12px/900/uppercase/2px tracking.

**Radii:** 6px (tight cards/tags) · 8px (event chips, buttons) · 10–12px (cards, panels).

**Layout:** content max-width **1080px** (marketing sections), 1100–1200px (app/header/footer). Section padding `40px 40px` desktop → `24px 16px` mobile.

**Breakpoints (as used in the real CSS):** `900px` (grids 3→2, filter→bottom-sheet), `768px` (main mobile: multi-col → 1-col, hero/type shrink, header inline-nav → row), `640px` / `560px` / `480px` (fine-tuning some grids). Match these when extending a component.

---

## Conventions

- **Class prefixes:** `dwl-` (marketing components, from about/resources/give), `cr-` (counseling topic index), `rsrc-` / `resource-` (leaders resources app), `site-` (header/footer). Keep them.
- **Class names must be unique on a page.** Two live components genuinely share the `dwl-feat-` prefix (about "Featured Events" vs resources "Other Content" list). In this library the events one was renamed `dwl-events-`. If you place both on one page, keep them under different prefixes.
- **Self-contained:** never rely on a component's classes existing elsewhere; each block carries its own `<style>`.
- **Don't hardcode a font import.** Circe/Lato come from the theme.
- **Scope new chrome** you add (like the gallery does with `#dwell-cl`) so it can't leak into site styles.

---

## Dependencies (what each component needs in its environment)

All of these are present site-wide on dwellcc.org, so components "just work" when pasted into a Rock block. Note them if building/previewing off-site.

| Needs | Components |
|---|---|
| jQuery + GSAP | `site-header` (dropdowns), `page-title` (collapsible mobile nav) |
| Font Awesome | `icon-cards` (`fal`), `resource-list` + `resource-cta` (`fa`) |
| theme.css (Bootstrap grid + footer styles) | `footer`, `page-title` (`.container`) |
| htmx (live filtering; static markup here) | `filter-panel`, `resource-list` "Show More" |

---

## Component index

IDs match `library.html` `<section id="…">`. "Use" = when to reach for it.

### Foundations
- **`colors`** — palette swatches (reference, not a page component).
- **`typography`** — Circe type scale reference.
- **`buttons`** — `.btn-dwell-primary` (green, uppercase, for "Add …") and `.dwl-give-btn` (lime, green-on-hover, primary CTA like "Give now").
- **`eyebrow`** — small uppercase green section label. Precedes most sections.

### Page sections
- **`hero`** — `.dwl-hero` dark teal page header w/ decorative circles. Top of a page. (Give variant adds `.dwl-give-btn` + note — see buttons.)
- **`notice`** — `.dwl-strip` thin deep-green member/login strip. Sits under the hero.
- **`page-title`** — `#pageNav` on-page title + subnav bar. Gray bar ≥1200px; tappable green collapsible bar below. Needs jQuery+GSAP.

### Cards
- **`feature-cards`** — `.dwl-intro` two-up promo cards (tag + heading + text + arrow CTA). "New here?" style intros.
- **`events`** — `.dwl-events` gradient banner + "Register now" tag + 3 event tiles. Time-bound promos.
- **`image-cards`** — `.dwl-meet` photo card w/ badge overlay + CTA. Visual callouts (Visit us / Home churches).
- **`link-cards`** — `.dwl-inv` heading + text + dot-separated links. "Get involved" style hubs.
- **`icon-cards`** — `.dwl-more` icon + title + text, 3-up. Secondary link grids. Font Awesome.
- **`intent-cards`** — `.dwl-intent` green left-border cards, first can span 2 (`--wide`). "Explore by interest" (5→3→2→1).
- **`type-cards`** — `.dwl-types` lime left-border cards, 4-up. Content types (Books / Podcasts …).

### Lists
- **`content-rows`** — `.dwl-feat` divider rows w/ colored type badge + title + meta. Mixed content lists.
- **`topic-tiles`** — `.cr-section` + `.cr-grid` + `.cr-card` grouped topic tiles w/ resource counts + hover accent. Counseling/leader topic indexes.

### Giving
- **`giving-cards`** — `.dwl-learn` two-up info cards (lime hover). Explaining giving options.
- **`split-rows`** — `.dwl-trans` info-left / link-right divider rows. Financial transparency, resource lists w/ actions.
- **`quote`** — `.dwl-quote` full-bleed dark scripture band (`100vw` + negative margins). Verse callouts.

### Resources app (leaders/resources browse UI)
- **`filter-panel`** — `.resource-filters` dark search + category/type/sort sidebar. Below 900px becomes a fixed bottom sheet on the live page.
- **`resource-list`** — `.rsrc-*` category-grouped result list w/ icon rows + "Show More". htmx-driven live.
- **`resource-cta`** — `.resource-cta__btn` dark pill → lime on hover. "Explore more" footer CTA.

### Site chrome
- **`site-header`** — `.site-header` global top bar (logo, nav, translate/search/account, mobile nav row). jQuery. The Google-CSE search overlay is a separate block, not included.
- **`footer`** — `.site-footer` global footer (address, link columns, social). Uses theme.css Bootstrap grid.

---

## Composition (typical page order)

A standard marketing page stacks like this (each is a copied component block):

```
site-header          (global; usually already on the Rock layout)
page-title           (optional on-page title/subnav)
hero                 (.dwl-hero)
notice               (.dwl-strip — optional)
[ content sections: feature-cards / events / image-cards /
  link-cards / icon-cards / intent-cards / type-cards /
  content-rows / topic-tiles / giving-cards / split-rows ]
quote                (.dwl-quote — optional closer)
footer               (global)
```

Sections use `max-width:1080px; margin:0 auto` and their own top/bottom padding, so they stack without wrappers. The `quote` and `hero` intentionally break full-width.

---

## Adding a new component

1. Build it self-contained (own `<style>`, Circe font, tokens above, a unique `dwl-`/`cr-`/etc. prefix, responsive at 768px minimum).
2. Add a `<section class="cl-block" id="{id}">` to `library.html` with an `<h2>`, a `<p class="cl-desc">`, and a `<div class="cl-demo"><template class="cl-src">…</template></div>` holding the real code. The gallery script auto-builds the Preview/Code/Copy UI.
3. Add it to the sidebar `<nav>` and to the **Component index** here.
