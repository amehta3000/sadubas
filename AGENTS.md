# AGENTS.md

This file provides guidance to Codex (Codex.ai/code) when working with code in this repository.

## What this is

A single-page static website for **SADUBAS — The Sadhus of Bass**, a Bollywood psychedelia duo (Ameet Mehta + Robin Sukhadia) based in Los Angeles, active since 1999.

## Structure

Everything lives in one file: `index.html` contains all HTML, CSS (in a `<style>` block), and JavaScript (in a `<script>` block at the bottom). There is no build step, no bundler, no package manager, and no framework.

Images go in an `images/` directory. The hero banner is expected at `images/hero-banner.png` (currently absent — the `.jpeg` files in the root are draft versions for reference). The gallery section has placeholder cells waiting for real photos.

## Development

Open `index.html` directly in a browser, or serve it locally:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

No compilation, no installs.

## Architecture / design system

**CSS custom properties** (`:root` in the `<style>` block) define the full color palette — `--gold`, `--red`, `--cream`, `--teal`, etc. All colors should come from these tokens.

**Typography** uses three Google Fonts:
- `Bebas Neue` — display/headings (`h1`, `h2`, `h3`, `.nav-logo`, `.footer-logo`)
- `Rajdhani` — body text, labels, nav links
- `DM Serif Display` — italic pull-quotes

**Section pattern**: each `<section>` contains a `.container` (max-width 1200px), a `.sh` section-header div with a `.label` + `.section-title` + `.rule`, then the section content. Sections alternate between `var(--black)` and `var(--dark)` backgrounds with `.deco-line` or `.stripe-bar` dividers.

**Scroll reveal**: elements get class `reveal` (plus optional `d1`–`d4` delay classes) and are triggered by an `IntersectionObserver` that adds class `in`.

## Content update patterns

**Adding a show** (Upcoming Shows section, `#shows`): copy a `.show-item` `<li>` and update the date block, title, venue, location, and ticket link. Use class `tickets` or `free` on `.show-link` depending on admission type. Past/placeholder items use `opacity: 0.45` inline style.

**Adding to Show History** (`#history`): add a `.history-item` inside the appropriate `.year-group`, or create a new `.year-group` with a `.year-label`.

**Adding gallery photos**: replace `<div class="gi-placeholder">Add Photo</div>` inside each `.gi` element with `<img src="images/your-photo.jpg" alt="description">`. The grid uses a 12-column layout with specific `grid-column: span N` assignments per nth-child — preserve the six `.gi` children if the layout is to stay intact.

**Hero image**: update the `url('images/hero-banner.png')` reference inside `.hero-bg` in the CSS.

**Adding a video**: the side panel has a `.video-coming` placeholder. A second embed would follow the same `.video-embed-wrap` + iframe pattern as the primary.
