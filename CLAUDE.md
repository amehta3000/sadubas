# CLAUDE.md

Guidance for Claude Code (claude.ai/code) when working in this repository.
`AGENTS.md` points here — keep this file as the single source of truth.

## What this is

Static site for **SADUBAS — The Sadhus of Bass**, a Bollywood psychedelia duo
(Ameet Mehta + Robin Sukhadia) based in Los Angeles, active since 1999.

Two independent pages, each fully self-contained — all HTML, CSS (`<style>` in
the head) and JS (`<script>` at the bottom) in one file. No build step, no
bundler, no package manager, no framework, no dependencies beyond Google Fonts.

| File | Purpose |
| --- | --- |
| `index.html` | Public site, served at **sadubas.com** |
| `label/index.html` | Private *Cinematica* label presentation. `noindex/nofollow`, not linked from anywhere on the public site — it is unlisted, not access-controlled. |

## Development

```bash
python3 -m http.server 8000
# http://localhost:8000  and  http://localhost:8000/label/
```

Serve rather than opening the files directly — the label page's audio player
and relative paths behave better over HTTP. No compilation, no installs.

## Deployment

`.github/workflows/` deploys to GitHub Pages on every push to `main`: the repo
root is uploaded verbatim, so anything committed is public (including the draft
`.jpeg` files in the root and everything in `images/`). `CNAME` pins the apex
domain `sadubas.com`.

## Images

Photos on the public site are served from **`https://media.sadubas.com/…`**, not
from the repo. `images/` holds the local originals (~80 MB) as an archive; they
are uploaded to that host separately and are *not* the copies the live site
loads. When adding a photo, upload it to `media.sadubas.com` first, then
reference the absolute URL.

The label page follows the same convention, but some cover art, artist imagery,
and Visual World items are still referenced as `../images/…` in `LABEL_DATA`
because they are not on the CDN yet. Move those references to the CDN when the
assets are uploaded.

The root `.jpeg` files (`full-page.jpeg`, `hero-v2.jpeg`, …) are draft mockups
kept for reference.

## Design system (both pages)

**Tokens** — `:root` custom properties define the palette: `--black` `--dark`
`--surface` `--surface2` `--gold` `--gold-lt` `--gold-dim` `--red` `--red-br`
`--teal` `--cream` `--cream-dim` `--white`. All colors come from these; don't
introduce literals.

**Typography** — Google Fonts:
- `Bebas Neue` — display/headings (`h1`, `h2`, `h3`, `.nav-logo`, `.footer-logo`)
- `Rajdhani` — body text, labels, nav links
- `DM Serif Display` — italic pull-quotes and editorial accents

**Texture** — an animated SVG film-grain overlay on `body::after` at 4% opacity.

**Scroll reveal** — elements get `reveal` (plus optional `d1`–`d4` delay
classes); an `IntersectionObserver` adds `in` when they enter the viewport.

## `index.html`

Sticky `#nav` over: hero (`#home`) → About (`#about`) → Videos (`#videos`) →
Upcoming Shows (`#shows`) → Show History (`#history`) → Gallery (`#gallery`) →
Connect (`#connect`) → Booking (`#booking`).

**Section pattern**: `<section>` → `.container` (max-width 1200px) → a `.sh`
header holding `.label` + `.section-title` + `.rule`, then the content.
Sections alternate `var(--black)` / `var(--dark)` backgrounds separated by
`.deco-line` or `.stripe-bar` dividers.

**Hero image**: the `url('https://media.sadubas.com/hero-banner.png')` reference
inside `.hero-bg`. Mobile uses a different `background-position` — both have
been tuned so neither face is cropped; re-check both breakpoints if you touch it.

**Adding a show** (`#shows`): copy a `.show-item` `<li>` and update the date
block, title, venue, location and ticket link. Use class `tickets` or `free` on
`.show-link` depending on admission. The list currently holds one dimmed "More
Shows Coming Soon" placeholder (`opacity: 0.45` inline) — replace it when real
dates land.

**Adding to Show History** (`#history`): add a `.history-item` to the right
`.year-group`, or create a new `.year-group` with a `.year-label`. Years run
2026 back to 2008, newest first.

**Adding a video** (`#videos`): `.video-main` holds the primary YouTube embed in
a `.video-embed-wrap`; `.video-side` holds three `.video-thumb` links using
`https://i.ytimg.com/vi/<ID>/mqdefault.jpg` thumbnails. Follow the existing
pattern for either.

**Adding gallery photos** (`#gallery`): add a `.gi` div containing an `<img>`
pointing at `media.sadubas.com`. The grid is 12-column with per-`nth-child`
`grid-column: span N` and `aspect-ratio` rules for children 1–11 (mobile
collapses to 2 columns). There are currently **11** cells — adding a 12th needs
a matching `nth-child` rule, and removing one reshuffles every later cell.

Note: the `.gi-placeholder` and `.video-coming` CSS rules are leftovers, no
longer referenced by any markup.

## `label/index.html`

A private one-page pitch for the unreleased album *Cinematica*, styled to match
the public site but with its own tokens and layout.

Nearly all copy and imagery is driven by a single **`LABEL_DATA`** object in the
script block — `hero`, `album` (artwork + story paragraphs), `tracks`, `video`,
`artist` (bio + members), `gallery`, `performances`, `partnership`, `downloads`,
`contact`. Edit that object rather than the markup; the JS renders it into
`id`-tagged containers on load. Sections use `.eyebrow` labels rather than the
public site's `.sh`/`.label` pattern.

The custom audio player streams the ten tracks listed in `LABEL_DATA.tracks`
from `/label/audio/*.mp3`. **Those files are not in the repo** — the player will
404 until they are added or the `src` values are repointed. Track durations are
still `"TBD"`, and most `downloads[].url` values are empty except the Dropbox
label kit.

Because Pages publishes the whole repo, anything dropped in `label/audio/` is
publicly reachable by URL.
