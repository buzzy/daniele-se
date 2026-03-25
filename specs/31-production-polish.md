# Production Quality Polish Layer

The details that separate a senior's work from a junior's work — and a 3-week project from a 3-month project. These specifications must be followed with zero deviation.

---

## 1. Typography Micro-Polish

### Font Stack

```css
--font-mono:    'JetBrains Mono', 'Fira Code', 'Cascadia Code', 'Consolas', monospace;
--font-display: 'Share Tech Mono', monospace;  /* section headings, prompts */
--font-body:    'JetBrains Mono', monospace;   /* ALL body copy — no serif fallback, ever */
```

### Font Feature Settings (Applied Globally — Never Omitted)

```css
body {
  font-feature-settings: 'kern' 1, 'liga' 1, 'calt' 1, 'tnum' 1, 'zero' 1, 'ss01' 1;
  font-variant-numeric: tabular-nums slashed-zero;
  text-rendering: optimizeLegibility;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}
```

`tnum` is non-negotiable: every number in htop bars, timestamps, PID columns, and uptime counters must align in columns. `zero` prevents the ambiguous 0/O problem on a terminal UI. `ss01` enables JetBrains Mono's alternate letterforms (cleaner `a`, `g`).

### Letter-Spacing and Line-Height Per Element

| Element | font-size | font-weight | letter-spacing | line-height |
|---|---|---|---|---|
| Terminal prompt (`$`) | 0.9375rem (15px) | 400 | 0 | 1.4 |
| Body / command output | 0.875rem (14px) | 400 | 0.01em | 1.6 |
| Section headers (man `.SH`) | 1rem (16px) | 700 | 0.12em | 1.2 |
| Sub-headers (`.SS`) | 0.875rem (14px) | 700 | 0.08em | 1.3 |
| htop column labels | 0.6875rem (11px) | 400 | 0.10em | 1.0 |
| Dossier card title | 1.125rem (18px) | 700 | 0.06em | 1.2 |
| Dossier card meta | 0.75rem (12px) | 400 | 0.05em | 1.5 |
| Status bar (nav bottom) | 0.6875rem (11px) | 400 | 0.08em | 1.0 |
| Boot sequence | 0.875rem (14px) | 400 | 0.02em | 1.5 |
| Error / warning output | 0.875rem (14px) | 400 | 0.01em | 1.6 |
| `CLASSIFIED` badge | 0.625rem (10px) | 700 | 0.20em | 1.0 |

Letter-spacing increases as font-size decreases — at 10–12px, 0.1–0.2em is required for legibility. Tight tracking on bold headers at large sizes is intentional — wide-tracked uppercase at 18px+ looks amateurish.

### Optical Alignment Details

- **Prompt `$` character:** `position: relative; top: 1px` — the descender well makes it read as floating against the cap-height of adjacent text without this nudge.
- **Hanging punctuation** on man page blockquotes: `hanging-punctuation: first last`. Fallback: `text-indent: -0.45em` on the container.
- **`[REDACTED]` blocks:** `letter-spacing: 0.3em` to stretch the block visually and fill the gap left by replaced text.
- **All focusable cards:** `outline` not `border` for focus rings — layout never shifts. `outline-offset: 2px` always.

---

## 2. Color Precision

### Complete Base Palette

```css
:root {
  /* Backgrounds — z-depth darkens */
  --bg-base:      #0a0c0f;  /* body, outermost frame */
  --bg-surface:   #0e1117;  /* primary panels, man page body */
  --bg-raised:    #131820;  /* dossier cards, htop rows */
  --bg-overlay:   #1a2030;  /* modals, autocomplete dropdown */
  --bg-inset:     #080a0d;  /* input fields, code blocks */
  --bg-selection: #1e3a5f;  /* text selection */

  /* Primary green */
  --green-dim:          #1a3d1a;
  --green-muted:        #2d6b2d;
  --green-base:         #39ff14;
  --green-bright:       #7fff5f;
  --green-glow:         rgba(57, 255, 20, 0.15);
  --green-glow-strong:  rgba(57, 255, 20, 0.35);

  /* Amber — warnings, secondary accent */
  --amber-dim:    #3d2a00;
  --amber-muted:  #7a5200;
  --amber-base:   #ffb300;
  --amber-bright: #ffd54f;
  --amber-glow:   rgba(255, 179, 0, 0.15);

  /* Red — errors, DANGER, kill signals */
  --red-dim:    #3d0000;
  --red-muted:  #7a0000;
  --red-base:   #ff2d2d;
  --red-bright: #ff6b6b;
  --red-glow:   rgba(255, 45, 45, 0.15);

  /* Cyan — info, hyperlinks, classification banners */
  --cyan-dim:    #003d3d;
  --cyan-muted:  #006666;
  --cyan-base:   #00e5ff;
  --cyan-bright: #80f0ff;
  --cyan-glow:   rgba(0, 229, 255, 0.15);

  /* Text roles */
  --text-primary:   #c8d8b0;  /* main body — warm green-gray, not pure white */
  --text-secondary: #6b7d6b;  /* metadata, timestamps, dimmed labels */
  --text-muted:     #3d4d3d;  /* placeholder, very dimmed content */
  --text-bright:    #e8f0e0;  /* emphasis, hover labels */
  --text-inverse:   #0a0c0f;  /* text on bright backgrounds */
  --text-link:      #00e5ff;
  --text-link-hover:#80f0ff;
  --text-error:     #ff2d2d;
  --text-warning:   #ffb300;
  --text-success:   #39ff14;
  --text-info:      #00e5ff;

  /* Borders */
  --border-dim:    #1a2530;
  --border-base:   #253040;
  --border-bright: #3a4f65;
  --border-focus:  #39ff14;
  --border-error:  #ff2d2d;
  --border-warning:#ffb300;

  /* CRT overlays */
  --scanline-color:   rgba(0, 0, 0, 0.08);
  --vignette-color:   rgba(0, 0, 0, 0.45);
  --phosphor-bloom:   rgba(57, 255, 20, 0.03);
}
```

### Z-Depth Background Rule

Every +1 z-level uses exactly the next `--bg-*` variable. Never skip a level. Never use `#000000` — the base is `#0a0c0f`.

| Z-level | Variable | Lightness delta |
|---|---|---|
| Base | `--bg-base` (#0a0c0f) | — |
| +1 Surface | `--bg-surface` (#0e1117) | +4L |
| +2 Raised | `--bg-raised` (#131820) | +5L |
| +3 Overlay | `--bg-overlay` (#1a2030) | +7L |
| Inset (below base) | `--bg-inset` (#080a0d) | -2L |

### Complete State Color Matrix

#### Terminal Input

| State | background | border | text | caret |
|---|---|---|---|---|
| default | `--bg-inset` | `--border-base` | `--text-primary` | `--green-base` solid |
| hover | `--bg-surface` | `--border-bright` | `--text-primary` | `--green-base` solid |
| focus | `--bg-inset` | bottom `--border-focus` 2px | `--text-bright` | `--green-base` blinking |
| disabled | `--bg-base` | `--border-dim` | `--text-muted` | none |
| error | `--bg-inset` | bottom `--border-error` 2px | `--text-primary` | `--red-base` |

#### Dossier Cards

| State | background | border | title | shadow |
|---|---|---|---|---|
| default | `--bg-raised` | `--border-base` | `--text-primary` | none |
| hover | `--bg-overlay` | `--cyan-muted` | `--cyan-base` | `0 0 12px --cyan-glow` |
| active | `--bg-surface` | `--cyan-base` | `--cyan-bright` | `0 0 20px rgba(0,229,255,0.25)` |
| focus-visible | `--bg-raised` | (outline `--border-focus`) | `--text-primary` | `0 0 0 2px --green-glow-strong` |
| locked | `--bg-base` | `--red-muted` | `--red-muted` | none |

#### htop Process Rows

| State | background | text | bar-fill |
|---|---|---|---|
| default even | transparent | `--text-primary` | `--green-base` |
| default odd | `rgba(255,255,255,0.015)` | `--text-primary` | `--green-base` |
| hover | `rgba(57,255,20,0.06)` | `--text-bright` | `--green-bright` |
| selected | `--bg-selection` | `--text-bright` | `--cyan-base` |
| high-CPU (>80%) | `rgba(255,45,45,0.08)` | `--text-primary` | `--red-base` |
| zombie (easter egg) | `rgba(255,179,0,0.06)` | `--amber-base` | `--amber-base` |

#### Navigation Buttons

| State | background | border | text |
|---|---|---|---|
| default | transparent | `--border-dim` | `--text-secondary` |
| hover | `rgba(57,255,20,0.05)` | `--green-muted` | `--green-base` |
| active | `rgba(57,255,20,0.12)` | `--green-base` | `--green-bright` |
| focus-visible | transparent | (outline `--border-focus`) | `--text-primary` |
| disabled | transparent | `--border-dim` | `--text-muted` |
| current | `rgba(57,255,20,0.08)` | `--green-muted` | `--green-base` |

---

## 3. Spacing System Discipline

Base unit: `8px` (`0.5rem` at 16px root). Every margin, padding, gap, and offset is a multiple of this unit. No exceptions. No `7px`, `10px`, `15px`.

```css
:root {
  --space-1:   4px;   /* 0.5× — intra-component micro-gaps only (icon↔label) */
  --space-2:   8px;   /* 1×  — badge padding, tag gap */
  --space-3:  12px;   /* 1.5× — htop rows, man page indentation only */
  --space-4:  16px;   /* 2×  — input padding, card inner x-axis */
  --space-5:  24px;   /* 3×  — card inner y-axis, section gaps */
  --space-6:  32px;   /* 4×  — between major sections, panel margins */
  --space-7:  40px;   /* 5×  — terminal top chrome height */
  --space-8:  48px;   /* 6×  — section vertical margin */
  --space-9:  64px;   /* 8×  — outermost body padding large screens */
  --space-10: 96px;   /* 12× — between independent views */
}
```

`--space-3` (12px) is the only non-power-of-2 step. Used in exactly three places: htop row height, man page `.RS` block inner padding, and boot sequence line gaps. Nowhere else.

### Component Spacing Assignments

| Component | Property | Value |
|---|---|---|
| Terminal window chrome | `height` | `--space-7` (40px) |
| Terminal chrome inner | `padding: 0 --space-4` | `0 16px` |
| Terminal content area (desktop) | `padding: --space-5 --space-6` | `24px 32px` |
| Terminal content area (mobile) | `padding: --space-4` | `16px` |
| Command input row | `padding: --space-2 0` | `8px 0` |
| Command input field | `padding: --space-2 --space-4` | `8px 16px` |
| Dossier card | `padding: --space-5 --space-6` | `24px 32px` |
| Dossier card header↔body gap | `margin-bottom` | `--space-4` (16px) |
| Dossier tag row | `gap` | `--space-2` (8px) |
| Dossier tag | `padding: --space-1 --space-2` | `4px 8px` |
| htop row height | `height` | `--space-5` (24px) |
| htop column header | `padding: --space-2 --space-4` | `8px 16px` |
| htop progress bar | `height` | `--space-2` (8px) |
| htop bar vertical margin | `margin: --space-1 0` | `4px 0` |
| Man page paragraph gap | `margin-bottom` | `--space-5` (24px) |
| Man page section header | `margin: --space-6 0 --space-4` | `32px 0 16px` |
| Man page `.RS` indent | `padding-left` | `--space-6` (32px) |
| Autocomplete item | `padding: --space-2 --space-4` | `8px 16px` |
| Status bar | `height` | `--space-5` (24px) |
| Status bar padding | `padding: 0 --space-4` | `0 16px` |
| Boot sequence line gap | `margin-bottom` | `--space-1` (4px) |
| Section separator | `margin: --space-6 0` | `32px 0` |
| Badge / chip | `padding: --space-1 --space-2` | `4px 8px` |
| Modal | `padding: --space-6` | `32px` |
| Card grid gap | `gap` | `--space-5` (24px) |

---

## 4. Border and Line Quality

### Four Border Widths — No Others Permitted

| Width | Variable | Usage |
|---|---|---|
| `1px` | `--border-thin` | All structural borders: cards, inputs, rows, dividers |
| `2px` | `--border-medium` | Focus rings, selected states, active accents |
| `3px` | `--border-thick` | Critical alerts, DANGER states, CLASSIFIED banners |
| `0` | — | Explicit removal (nav buttons that use background only) |

Never `0.5px` — sub-pixel rendering is inconsistent and looks accidental.

### Border Style Rules

**`solid`** — default for all structural borders: terminal frame, cards, inputs, modals. Solid = tangible structure.

**`dashed`** — empty state slots, placeholder zones, `[REDACTED]` underlines, drop targets.

**`dotted`** — used for exactly one thing: the `::before` corner connectors on dossier cards (circuit-board aesthetic). Nowhere else.

**`double`** — never used. Looks like a CSS accident in this aesthetic.

### Corner Radius: Total Zero

```css
--radius-none: 0;     /* every interactive and structural element */
--radius-sm:   2px;   /* progress bar fill caps only */
```

`border-radius: 0` on everything: buttons, cards, inputs, modals, dropdowns, tags, tooltips. The terminal aesthetic requires hard corners. The moment a rounded corner appears, the "this is a real terminal" illusion breaks.

The `2px` on progress bar fills is the only exception: a 0-radius fill in a 0-radius container creates a 1px aliasing artifact on non-retina displays. 2px on the fill only — invisible against the container's square corners — eliminates this.

### Key Border Declarations

```css
/* Terminal window outer frame */
.terminal-viewport { border: 1px solid var(--border-base); }
.terminal-viewport:focus-within { border-color: var(--border-bright); }

/* Input — bottom-only emphasis */
input { border: 1px solid var(--border-base); border-bottom-color: var(--border-bright); }
input:focus { border-color: var(--border-dim); border-bottom: 2px solid var(--green-base); outline: none; }

/* htop — bottom only, no side borders */
.htop-row { border-bottom: 1px solid var(--border-dim); }

/* CLASSIFIED banner */
.classified-stamp { border: 3px solid var(--red-base); }

/* Empty dossier slot */
.card-empty { border: 1px dashed var(--border-dim); }

/* Section divider with gradient fade */
.terminal-divider {
  height: 1px;
  background: linear-gradient(to right,
    transparent, var(--border-base) 15%, var(--border-bright) 50%,
    var(--border-base) 85%, transparent);
  border: none;
}
```

---

## 5. Interactive Feedback — Complete State Matrix

Every interactive element has all four required states. Mouse users never see focus rings. Keyboard users always see them (`:focus-visible` only, never `:focus`).

### Terminal Command Input

| State | CSS |
|---|---|
| default | `caret-color: var(--green-base)` |
| hover | `border-bottom-color: var(--green-muted)`, `background: var(--bg-surface)` |
| focus-visible | `border-bottom: 2px solid var(--green-base)`, caret blinks 530ms |
| disabled | `opacity: 0.4`, `cursor: not-allowed`, caret hidden |
| error | `border-bottom: 2px solid var(--red-base)`, `caret-color: var(--red-base)` |

### Navigation Command Buttons

| State | CSS |
|---|---|
| hover | `color: var(--green-base)`, `border-color: var(--green-muted)`, `background: rgba(57,255,20,0.05)`, 120ms |
| active | `color: var(--green-bright)`, `background: rgba(57,255,20,0.12)`, `transform: translateY(1px)` |
| focus-visible | `outline: 2px solid var(--green-base)`, `outline-offset: 2px` |
| disabled | `color: var(--text-muted)`, `opacity: 0.5`, `cursor: not-allowed` |

### Dossier Cards

| State | CSS |
|---|---|
| hover | `border-color: var(--cyan-muted)`, title `color: var(--cyan-base)`, `box-shadow: 0 0 12px rgba(0,229,255,0.12)`, 200ms |
| active | `box-shadow: 0 0 20px rgba(0,229,255,0.25)`, `transform: translateY(-1px)` (lift, not depress) |
| focus-visible | `outline: 2px solid var(--green-base)`, `outline-offset: 3px` |
| disabled/locked | `cursor: not-allowed`, `border-color: var(--red-muted)`, `filter: brightness(0.7)` |

### htop Process Rows

| State | CSS |
|---|---|
| hover | `background: rgba(57,255,20,0.06)`, `cursor: default` (not pointer — this is a terminal) |
| active | `background: rgba(57,255,20,0.10)` |
| focus-visible | `outline: 2px solid var(--green-base)`, `outline-offset: -2px` (inset) |
| selected | `background: var(--bg-selection)`, persists until ESC or other row selected |

### Autocomplete Suggestions

| State | CSS |
|---|---|
| hover | `background: rgba(57,255,20,0.07)`, `color: var(--text-primary)`, `border-left: 2px solid var(--green-muted)` |
| active | `background: rgba(57,255,20,0.14)`, `color: var(--green-base)` |
| keyboard-selected | `background: rgba(57,255,20,0.12)`, `color: var(--green-base)`, `border-left: 2px solid var(--green-base)` |

### SIGKILL / Danger Button (htop easter egg)

| State | CSS |
|---|---|
| hover | `color: var(--red-base)`, `border-color: var(--red-muted)`, `background: rgba(255,45,45,0.08)`, **80ms** (faster = urgency affordance) |
| active | `color: var(--red-bright)`, `background: rgba(255,45,45,0.18)`, `transform: scale(0.97)` |
| focus-visible | `outline: 2px solid var(--red-base)`, `outline-offset: 2px` |
| disabled (after kill) | `opacity: 0.35`, `text-decoration: line-through`, `cursor: not-allowed` |

### Man Page Links

| State | CSS |
|---|---|
| default | `color: var(--cyan-base)`, `text-decoration: underline dashed 1px`, `text-underline-offset: 3px` |
| hover | `color: var(--cyan-bright)`, `text-decoration: underline solid 1px` |
| active | `color: var(--text-bright)`, `text-decoration: underline solid 2px` |
| visited | `color: var(--cyan-muted)` — meaningful in navigation context |
| external | `::after { content: ' ↗'; font-size: 0.75em; color: var(--text-secondary); }` |
| focus-visible | `outline: 2px solid var(--cyan-base)`, `outline-offset: 2px` |

### Copy-to-Clipboard Button

| State | CSS |
|---|---|
| default | `opacity: 0` — visible only on parent hover (disclosure pattern) |
| parent hover | `opacity: 1`, 150ms |
| button hover | `color: var(--green-base)`, `background: rgba(57,255,20,0.08)` |
| active | `transform: scale(0.92)`, 92ms |
| success | `color: var(--green-bright)`, icon → checkmark, reverts after 2000ms |
| focus-visible | `outline: 2px solid var(--green-base)`, `opacity: 1` always |

### Transition Duration Rules

Only these durations are used for interactive state transitions. No others.

| Duration | Use |
|---|---|
| `80ms ease` | Danger elements, active/pressed states |
| `150ms ease` | Hover on small elements (tags, buttons, links) |
| `200ms ease` | Hover on large elements (cards, panels) |
| `300ms ease-out` | Enter/exit, dropdown open, panel slide |
| `530ms step-end` | Cursor blink (VT100 rate) |

`ease` not `ease-in-out` — the latter is sluggish at ≤200ms.

---

## 6. Content Width and Reading Line Length

### The Constraint

A terminal line is never wider than 80 characters at the working font size. This is the hard historical limit of 80-column terminals (VT100, 1978). Violating it breaks the simulation.

`80ch` at 14px JetBrains Mono ≈ 672px.

### Max-Width by Content Type

| Content type | max-width | Reason |
|---|---|---|
| Terminal window | `100vw` | The terminal IS the window |
| Terminal output text | `80ch` | 80-column terminal canon |
| Command input | `76ch` | 80ch minus `4ch` prompt (`$ `) |
| Man page body | `72ch` | Historic `nroff` default |
| Man page indented `.RS` | `64ch` | 72ch minus 8ch indent |
| Dossier card title | `56ch` | Prevents orphaned single words at 2-column grid |
| Dossier card description | `60ch` | Body copy needs more room than titles |
| htop table | `100%` of panel | Tabular data uses all available column space |
| htop process name column | `24ch` | Standard `ps`/`htop` behavior |
| Boot sequence | `80ch` | Matches terminal output rule |
| Error message | `72ch` | Errors are read, not scanned |
| Modal | `min(640px, 100vw - 64px)` | Never narrower than 32px padding each side |
| Autocomplete dropdown | `80ch` | Aligned to parent input |

### Why `ch` Units, Not `px`

`1ch` scales with font size at all zoom levels. A `max-width: 80ch` at 150% browser zoom remains exactly 80 characters wide. A `px`-based equivalent (`672px`) becomes misaligned at non-default font sizes. On a codebase simulating a real terminal, `ch` units for line-length constraints are a correctness requirement, not optional polish.

```css
.terminal-viewport    { width: 100vw; height: 100svh; }
.terminal-output      { max-width: 80ch; width: 100%; }
.man-page-body        { max-width: 72ch; width: 100%; }
.dossier-card__title  { max-width: 56ch; }
```

`100svh` (small viewport height) not `100vh` — handles mobile browser chrome correctly.

At viewport widths below `~705px`, the `80ch` constraint is dropped and `width: 100%` takes over — the screen itself becomes the 80-column constraint. No media query needed.
