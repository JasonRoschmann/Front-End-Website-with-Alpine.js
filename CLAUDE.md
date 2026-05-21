# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project shape

A single static HTML page demoing a photo-and-caption feed UI ("Awesome"). The entire app — markup, styles, and interactivity — lives in `index.html`. There is no build system, no package manifest, no test suite, and no lint configuration. All dependencies are pulled in via CDN at runtime:

- **Tailwind CSS** via `https://cdn.tailwindcss.com` (in-browser JIT — `@apply` works inside the inline `<style type="text/tailwindcss">` block)
- **Alpine.js 3.x** via `https://cdn.jsdelivr.net/npm/alpinejs@3.x.x/dist/cdn.min.js` (loaded with `defer`)
- **Google Fonts** (Lobster) and **icons8** images

## Running / iterating

Open `index.html` directly in a browser, or serve the directory with any static server (e.g. `python3 -m http.server`) and reload after edits. There is nothing to build, install, or test.

## Architecture conventions

**Single-file structure.** All component markup, Tailwind config (CSS variables, `@apply` rules, keyframes), and Alpine state declarations live inline in `index.html`. When adding features, follow the existing pattern rather than splitting into separate files — there's no module system here.

**Custom HTML tags as semantic containers.** The page uses non-standard tags like `<logo>`, `<hero>`, `<content>`, `<mobileicon>`, `<mobileSidebarNav>` as block-level wrappers for layout regions. These are not Web Components — they're unknown elements treated as generic containers and styled with Tailwind utility classes. Keep this convention when extending sections.

**Alpine state is scoped per region.** Each interactive area declares its own `x-data` on the wrapping element:
- `<header x-data="{ mobilenavOpen: false }">` — mobile nav toggle
- The user dropdown `<li x-data="{ dropdownOpen: false }">` — uses `@click.away` to close
- `<content x-data="{ mobileSidebarOpen: false }">` — mobile categories sidebar

There is no global Alpine store; state never crosses these region boundaries. When adding interactivity, prefer a new local `x-data` scope over hoisting state.

**`x-cloak` is required on any element hidden by `x-show` initially.** The inline `[x-cloak] { display: none !important; }` rule prevents a flash of unhidden content before Alpine boots. Every `x-show` element that starts hidden in this file also carries `x-cloak` — follow that pattern.

**Transitions use the `x-transition:enter-*` directive form** (not the shorthand). The header nav and dropdown both animate `-translate-y` and opacity over ~300ms — match that timing when adding similar entrances for visual consistency.

**Responsive pattern.** Mobile-first, with `md:` breakpoint flipping layouts. The mobile nav and sidebar use `x-show` for toggle behavior on small screens but force visibility on `md:` and up via `md:!block` (Tailwind important modifier overriding Alpine's inline `display: none`). Preserve this pattern when adding collapsible regions.

## Design tokens

CSS custom properties in `:root` define the theme:
- `--primary: rgb(88,40,244)` / `--primary-hover: rgb(69,29,200)` — used by `.button`
- `--font1: 'Lobster', sans-serif` — exposed as the `.font1` utility and applied to `h1`

Reusable component classes defined in the inline `<style type="text/tailwindcss">` block: `.button`, `.card`, `.navitems`, `.hoverlist`, `.highlight`, `.titleBlip`. Prefer extending these over inlining one-off styles when the same pattern appears in multiple places.

## Git workflow

Active development branch for this environment: `claude/add-claude-documentation-Zzk1V`. Push to that branch and open a draft PR against the default branch when work is complete.
