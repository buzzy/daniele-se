# Project Showcase — Classified Dossier Cards

## Concept
Projects are presented as "classified mission files" or declassified dossiers. Each card looks like an official document printed by a government or high-security system, consistent with the overall sci-fi/terminal aesthetic.

## Card Anatomy
Each project card contains:
- **Codename** — Primary headline (can be the real project name or a stylized version)
- **Reference ID** — Fake alphanumeric ID (e.g. `PRJ-0042`)
- **Status Badge** — Stamped label: `DEPLOYED` (green) / `ACTIVE` (cyan) / `ARCHIVED` (amber) / `CLASSIFIED` (red)
- **Description** — 2–3 sentence plain-language summary of what the project does
- **SYSTEMS USED** — Tech stack listed in small-caps monospace
- **Links** — GitHub repo and/or live demo (styled as `[SOURCE]` and `[DEMO]` buttons)
- **Date** — "Mission initiated: YYYY"

## Layout
- Responsive grid: 3 columns desktop, 2 tablet, 1 mobile
- Cards have a slight paper/panel texture with glowing border
- Monospace font throughout
- Optional: a faint watermark or diagonal `DECLASSIFIED` stamp across the card background

## Hover State
- Scanline sweep animation across the card
- Border glow intensifies
- Subtle screen-flicker glitch on the border (not text — keeps it readable)

## Filtering
- Filter buttons styled as terminal function keys: `[ALL]` `[ACTIVE]` `[DEPLOYED]` `[ARCHIVED]`
- Optionally filterable by technology (clicking a tech tag in SYSTEMS USED filters to matching projects)

## Featured Project
- One project can be pinned as "PRIORITY-1" — displayed full-width above the grid with a more detailed breakdown

## Project Count
- No hard limit — the grid handles any number
- If more than ~12 projects, consider pagination or a "LOAD MORE" command-style button

## Empty State
- If no projects match a filter: display `No records matching query. Try: ls --all`
