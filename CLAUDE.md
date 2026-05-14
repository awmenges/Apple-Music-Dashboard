# Apple Music Dashboard — CLAUDE.md

## About You (the developer)
You are a beginner coder. Always explain new concepts in plain language without assuming framework knowledge. Briefly define any technical term before using it.

## Project Overview
A personal music dashboard showing your Apple Music/Last.fm listening data. It pulls data from the **Last.fm API** using username **`awmenges1990`** and displays top artists, tracks, albums, recent scrobbles, and listening activity charts.

- Live URL: https://apple-music-dashboard.vercel.app/
- Deploys automatically to Vercel on every push to the `main` branch on GitHub

## Tech Stack
- **Pure HTML/CSS/JavaScript** — no React, no build step, no package.json
- **Single file**: everything lives in `index.html` (HTML, all CSS in `<style>`, all JS in `<script>`)
- **Charting**: hand-built CSS bar charts (divs with dynamic heights) — no Chart.js, no D3
- **Fonts**: Playfair Display (serif, for headings) + DM Mono (monospace, for everything else) via Google Fonts
- **External APIs**: Last.fm API (scrobble data) + iTunes Search API (30-second song previews)

## Design Tokens (CSS Variables)
All colors/sizes are defined at the top of the `<style>` block in `:root {}`. Always use these variables instead of hard-coded values:
- `--red: #fc3c44` — primary accent / active states
- `--bg: #000000` — page background
- `--s1 / --s2 / --s3` — card surface colors (progressively lighter)
- `--text / --t2 / --t3` — text at full / 55% / 28% opacity
- `--border` — subtle border color
- `--r / --rsm` — border radii (14px / 8px)

## Code Conventions
- API calls go through the `lfm(params)` helper function — never call `fetch` directly for Last.fm
- Skeleton loading states use the `.sk` class (shimmer animation) — show before data loads, hide after
- New content gets `class="... fu"` (fade-up animation) with `animationDelay` staggered by index
- Period buttons use `data-p` with values: `7day`, `1month`, `6month`, `12month`, `overall`
- The `period` variable holds the current selection
- Load functions are async, named `loadXxx()`, called together in `loadAll()`
- The period selector drives "Top This Period" and "Top 10" — the two charts on page 1 are hardcoded windows (always last 7 days and last 12 months), not period-driven

## Last.fm API Gotchas
- Base URL: `https://ws.audioscrobbler.com/2.0/`
- API key is hardcoded in JS — it's a public read-only key, not a secret
- `user.getrecenttracks` with `from`/`to` unix timestamps: the total scrobble count is in `recenttracks['@attr'].total`
- Max 200 results per page — use `page` param for pagination
- Rate limit: ~5 requests/second — avoid firing many parallel requests
- Track timestamps are in `date.uts` (Unix, UTC) — use `new Date(uts * 1000)` to get a JS Date; `.getDay()` and `.getHours()` return LOCAL time
- The "now playing" track has no `date` field and `@attr.nowplaying === 'true'`
- Last.fm `period` values for top-X endpoints: `7day`, `1month`, `6month`, `12month`, `overall`

## How to Run Locally
No build step needed — just open the file:
```
# Simplest: double-click index.html to open in browser

# Or serve locally (avoids some CORS edge cases):
npx serve .         # requires Node.js
python -m http.server 8080
```

## Deployment
- GitHub repo: https://github.com/awmenges/Apple-Music-Dashboard
- Hosted on **Vercel** at https://apple-music-dashboard.vercel.app/
- **Workflow**: develop on a feature branch → push to `testing` branch for review → merge to `main` for production
- Push to `main` → Vercel auto-deploys. No build command needed — Vercel serves static files as-is

## Current Status (as of 2026-05-13)
The `testing` branch has a completed but **not yet reviewed** Habits page (page 2). The user has edits they want to make to it before merging to `main`.

**What's on `testing` that isn't on `main` yet:**
- Page navigation in the header ("Top Music" / "Habits" pill buttons)
- Habits page with 4 sections:
  1. This-period vs last-period play count comparison card
  2. Activity over time bar chart (auto-granularity per period)
  3. Day-of-week average plays bar chart (Mon–Sun)
  4. 7×24 day/hour heatmap with red intensity scale
- In-session scrobble cache (`habitsCache`) keyed by period
- `CLAUDE.md` (this file)

**Next session:** the user wants to make edits to the Habits page design/content before merging. Start by asking what changes they have in mind, then work on the `testing` branch.
