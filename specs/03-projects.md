# Project Showcase — Classified Dossier Cards

## Concept
Projects are presented as "classified mission files" or declassified dossiers. Each card looks like an official document printed by a high-security system, consistent with the overall sci-fi/terminal aesthetic.

---

## Card Anatomy

Each project card contains:
- **Codename** — Primary headline (stylized/dramatic version of the project name)
- **Reference ID** — Fake alphanumeric ID (e.g. `PRJ-0042`)
- **Real Name** — Subtitle, smaller than codename
- **Status Badge** — Stamped label: `DEPLOYED` (green) / `ACTIVE` (cyan) / `ARCHIVED` (amber) / `CLASSIFIED` (red)
- **Domain Tag** — Industry/domain label (e.g. `FINTECH`, `DEVOPS-TOOLING`, `E-COMMERCE`, `REAL-TIME`) — used by potential clients to self-filter
- **Description** — 2–3 sentence plain-language summary
- **SYSTEMS USED** — Tech stack in small-caps monospace
- **Links** — `[SOURCE]` (GitHub) and `[DEMO]` (live demo) — hidden if status is `CLASSIFIED`
- **Year** — "Mission initiated: YYYY"

---

## Card Layout

- Responsive grid: 3 columns desktop, 2 tablet, 1 mobile
- Slight paper/panel texture with glowing border (category color)
- Monospace font throughout
- Optional faint `DECLASSIFIED` diagonal watermark across card background
- All information visible on the card without expanding

---

## Hover State

- Scanline sweep animation across the card
- Border glow intensifies
- Subtle screen-flicker glitch on border (not on text)

---

## Click → Project Detail Modal

Clicking a card opens a full-screen modal overlay. No URL change — the modal is a UI layer.

### Open Animation
- Backdrop: darkened with semi-transparent black + scanline texture
- Modal: expands from small rectangle to full size via scale + opacity (~300ms) with a brief CRT flicker

### Modal Content (top to bottom)
1. **Header bar** — `[PROJECT DOSSIER — PRJ-0042]` left; blinking cursor; `[CLOSE ✕]` right
2. **Codename** — Large, glitch-styled heading
3. **Real name** — Subtitle + status badge
4. **Classified separator rule**
5. **Full description** — Several paragraphs (expanded from card summary)
6. **SYSTEMS USED** — Tech stack as monospace badge chips
7. **MISSION LOG** — Bullet-point timeline of milestones (e.g. `2021-03 — Initial architecture deployed`)
8. **[SOURCE]** and **[DEMO]** links as large terminal-style buttons
9. **Footer** — Reference ID, year, `CLASSIFICATION LEVEL` (PUBLIC / INTERNAL / RESTRICTED)

### Close Behavior
- Click backdrop → close
- Press Escape → close
- Click `[CLOSE ✕]` → close
- Reverse animation on close (~200ms)
- Body scroll locked while modal open

### Back Button
- Browser back button does NOT close the modal (URL unchanged — intentional)

### Mobile Modal
- Full-screen (100% viewport) — slide-up from bottom (bottom sheet style)
- Close button pinned to top-right, visible while scrolling
- Content scrollable within modal

### Accessibility
- `role="dialog"`, `aria-modal="true"`, `aria-labelledby` pointing to codename heading
- Focus trapped inside modal while open
- Focus returns to the clicked card on close
- All elements keyboard-navigable in logical order

---

## Featured Project

- One project with `featured: true` in JSON displayed full-width above the grid
- Shows the full card content + a brief additional highlight stat or quote
- Labeled `PRIORITY-1 // FEATURED OPERATION`

---

## Filtering

- Filter buttons styled as terminal function keys: `[ALL]` `[ACTIVE]` `[DEPLOYED]` `[ARCHIVED]`
- Optional: domain tag filters (FINTECH, DEVOPS, etc.) if projects span many domains
- Filter buttons on mobile: horizontal scrollable pill row

---

## Empty State

```
No records matching query.
Try: ls --all
```

---

## Data Source

Content driven by `/data/projects.json`. Schema defined in `12-content-management.md`.
