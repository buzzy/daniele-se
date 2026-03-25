# Navigation — Tmux-Style Status Bar

## Layout & Dimensions

- Fixed to bottom of viewport, 100vw wide
- Height: `28px` desktop, `56px` mobile (two rows)
- Background: `#1a1a1a` (slightly lighter than body bg)
- Font: `JetBrains Mono`, fallback `monospace`
- Font size: `12px` desktop, `13px` mobile
- No border-radius, no box-shadow — sharp edges only
- Overflow hidden; no word wrap

## Three-Zone Structure

```
[LEFT ZONE — 180px]      [CENTER ZONE — flex-grow]      [RIGHT ZONE — 260px]
daniele@portfolio:~█  |  ~  about  projects  contact  |  ↑42d 7h | ● OPEN TO WORK | gh:dtesta
```

---

## LEFT ZONE — Session Label

**Content:** `daniele@portfolio:~█` (blinking block cursor)

- `daniele`: `#00ff9f` (terminal green)
- `@portfolio:~`: `#e0e0e0` (near-white)
- `█` cursor: CSS `animation: blink 1s step-end infinite`; disabled if `prefers-reduced-motion`
- Not clickable
- Mobile (< 380px): truncated to `d@p:~`

---

## CENTER ZONE — Section Tabs

**Tabs in order:**

| Tab | Label | Target |
|---|---|---|
| 0 | `~` | Top / Hero |
| 1 | `about` | About section |
| 2 | `projects` | Projects section |
| 3 | `contact` | Contact section |

**Separator between tabs:** ` | ` in `#444444`

**Inactive tab:** `color: #888888`, `background: transparent`
**Hover (non-active):** `color: #e0e0e0`, `background: #2a2a2a`, `cursor: pointer`
**Active tab:** `color: #000000`, `background: #00ff9f`, `padding: 0 6px`

**Click behavior:**
- Smooth scroll to section anchor
- Active tab updates immediately on click

**Passive active state:**
- IntersectionObserver watches all sections
- Whichever section occupies > 50% of viewport = active tab
- Never empty: if no section in view, last active tab stays highlighted
- `~` becomes active when scrolled above the About threshold

**Keyboard shortcuts:**
- `Alt+1` (/ `Ctrl+1` on macOS) → Hero
- `Alt+2` (/ `Ctrl+2`) → About
- `Alt+3` (/ `Ctrl+3`) → Projects
- `Alt+4` (/ `Ctrl+4`) → Contact
- `Ctrl+Tab` → cycle forward through sections
- `Ctrl+Shift+Tab` → cycle backward
- All shortcuts `preventDefault`; only active when focus is not inside a text input or textarea

**Focus ring:** `outline: 1px solid #00ff9f; outline-offset: -1px` (inside element, no layout shift)

---

## RIGHT ZONE — Three Elements

**Order:** `[uptime] | [availability] | [github]` — pipe separators in `#444444`

### Element 1: Uptime Counter

**Format:** `↑42d 7h 03m`

- `↑` glyph: `#00ff9f`
- Counter text: `#888888`
- Epoch: site launch date (hardcoded constant) — "site has been running for X"
- Updates every 60 seconds via `setInterval`
- Days are maximum unit (e.g. `↑400d 2h 11m` — never converts to years)
- Minutes zero-padded to 2 digits
- Hover tooltip (desktop): `"Site uptime since launch"` via `title` attribute

### Element 2: Availability Badge

| Config value | Rendered text | Color |
|---|---|---|
| `"open"` | `● OPEN TO WORK` | `#00ff9f` |
| `"unavailable"` | `● NOT AVAILABLE` | `#ff4444` |
| `"limited"` | `● CONTRACT ONLY` | `#ffb300` |

- `●` dot: pulsing CSS animation `opacity 1→0.3→1`, 2s loop (disabled if `prefers-reduced-motion`)
- Controlled entirely by `availabilityStatus` in `/data/config.json`
- Hover tooltip: `"Availability status"`

### Element 3: GitHub Handle

**Content:** `gh:dtesta` (replace with Daniele's actual GitHub handle)

- `gh:` in `#444444`, handle in `#e0e0e0`
- Clickable: opens GitHub profile in new tab (`target="_blank" rel="noopener noreferrer"`)
- Hover: handle underlined, `cursor: pointer`

---

## Mobile Layout (< 768px) — Two Rows, 56px total

**Row 1 (top):** CENTER ZONE only — tabs centered, full width
**Row 2 (bottom):** LEFT ZONE left-aligned + RIGHT ZONE right-aligned (compacted)

**Mobile right zone:**
- Uptime: hidden entirely
- Availability: `●` dot only (no text); long-press shows full status via `title`
- GitHub: `gh:dtesta` at `11px`

**Touch targets:** Each tab ≥ 44×36px

---

## Edge Cases

| Case | Behavior |
|---|---|
| Two sections at viewport boundary simultaneously | Higher IntersectionRatio wins |
| Page load with no hash | `~` tab active by default |
| Page load with hash (e.g. `#projects`) | That tab active on mount, before scroll events |
| Browser back/forward | `popstate` listener updates active tab to match hash |
| Very long uptime | `↑400d 2h 11m` — no truncation, days only |
| Desktop viewport 768–900px | Tab padding reduced to `0 3px`; labels never abbreviated |
| Right zone overflow | Availability badge collapses to dot-only (`●` only) |
| Font load failure | Falls through chain to system monospace — layout preserved |
| `prefers-reduced-motion` | Blinking cursor → static; availability pulse → static; scroll → instant jump |
| Forced colors / high contrast | Active tab uses system highlight; bar remains readable |
