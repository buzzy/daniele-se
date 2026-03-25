# Visual Design System

## Ambient Background

### Canvas Layer
A single `<canvas>` element, `position: fixed`, `z-index: 0`, `pointer-events: none`, full viewport.

Two elements rendered together:

**1. Falling character rain (canvas)**
- Characters: ASCII printable range + handful of katakana + hex digits `0-9A-F`
- Size: `10px` monospace per character
- Column spacing: `20px`
- Active columns: ~30–40% at any time (sparse, not dense)
- Trail length: 12–16 characters, fading from bright lead to near-invisible tail
- Leading character: flashes `#00ff9f` for one tick then drops to stream color
- Speed: capped at **24fps** (42ms per tick) — intentionally "old hardware" feel
- Stream lifetime: 60–180 ticks (randomized), then resets to new random column
- No horizontal movement — strictly vertical only

**2. Static grid underlay (CSS)**
```css
background-image:
  linear-gradient(rgba(255,255,255,0.03) 1px, transparent 1px),
  linear-gradient(90deg, rgba(255,255,255,0.03) 1px, transparent 1px);
background-size: 40px 40px;
```
Zero animation cost. Provides depth and a coordinate-system feel.

### Performance Rules
- `will-change: transform` on canvas element
- On `visibilitychange` (tab hidden): suspend animation loop via `cancelAnimationFrame`; resume on focus
- On `prefers-reduced-motion: reduce`: character rain **disabled**; static grid remains
- On mobile (< 768px): column count halved, trail length reduced to 8
- Target: < 4% CPU on mid-range laptop during animation
- Canvas resized only on `debounced resize` (300ms debounce) — no continuous polling

### Section Behavior
Background is identical across all sections. Section panels are semi-transparent overlays (`rgba` surface color) — rain is always partially visible through them. The background is the "desktop" of the terminal OS metaphor.

---

## Color Tokens

| Token | Hex | Usage |
|---|---|---|
| `--color-bg` | `#0a0c0f` | Body background, canvas base |
| `--color-surface` | `#0d1117` | Panel/card backgrounds (`rgba(13,17,23,0.88)`) |
| `--color-surface-raised` | `#161b22` | Hover states, focused elements |
| `--color-border` | `#21262d` | Panel borders, dividers |
| `--color-border-accent` | `#30363d` | Secondary borders, table lines |
| `--color-green-primary` | `#00ff9f` | Primary accent — active nav, CTAs, headings |
| `--color-green-dim` | `#00cc7a` | Body text, secondary labels |
| `--color-green-muted` | `#005c38` | Disabled/inactive states, placeholders |
| `--color-cyan` | `#00d4ff` | Links, secondary accent, timestamps |
| `--color-cyan-dim` | `#0099bb` | Visited links, secondary data |
| `--color-amber` | `#ffb300` | Warnings, badges, "current" indicators |
| `--color-amber-dim` | `#cc8800` | Amber hover state |
| `--color-red` | `#ff4444` | Errors only — use sparingly |
| `--color-white` | `#e6edf3` | Maximum contrast text (never pure `#ffffff`) |
| `--color-rain-lead` | `#00ff9f` | Rain stream leading character |
| `--color-rain-mid` | `#00994d` | Rain stream mid-trail |
| `--color-rain-tail` | `rgba(0,153,77,0.15)` | Rain stream fading tail |

---

## Typography

**Primary font:** `JetBrains Mono` (Google Fonts, weights 400 and 700 only)
**Fallback:** `'Cascadia Code', 'Fira Code', 'Consolas', 'Liberation Mono', monospace`

All text is monospace. No exceptions anywhere on the site.

| Token | Size | Weight | Line Height | Usage |
|---|---|---|---|---|
| `--text-xs` | `11px` | 400 | 1.4 | Timestamps, metadata, nav labels |
| `--text-sm` | `13px` | 400 | 1.5 | Body copy, list items, descriptions |
| `--text-base` | `15px` | 400 | 1.6 | Default paragraph text |
| `--text-md` | `18px` | 700 | 1.4 | Section subheadings, project titles |
| `--text-lg` | `24px` | 700 | 1.2 | Section headings |
| `--text-xl` | `36px` | 700 | 1.1 | Hero name display |
| `--text-2xl` | `56px` | 700 | 1.0 | Single impact line on hero only |

- Letter-spacing: `0.05em` on `--text-lg` and above; `0em` everywhere else
- Text transform: section headings and nav items are `uppercase`; body text is never uppercased

---

## Spacing System

Base unit: `8px`. All spacing is multiples of this unit only.

| Token | Value | Usage |
|---|---|---|
| `--space-1` | `4px` | Tight internal padding, badge gaps |
| `--space-2` | `8px` | Default inline gap, icon-to-label |
| `--space-3` | `12px` | Small component internal padding |
| `--space-4` | `16px` | Standard component padding |
| `--space-5` | `24px` | Card padding, section element gaps |
| `--space-6` | `32px` | Between cards in grid |
| `--space-7` | `48px` | Section internal top/bottom padding |
| `--space-8` | `64px` | Between major sections |
| `--space-9` | `96px` | Page-level vertical rhythm |

---

## Border System

All borders: `1px solid` — no `2px`, no `3px`. **No rounded corners** except one exception.

| Token | Value | Usage |
|---|---|---|
| `--border-default` | `1px solid #21262d` | Panel edges, card borders |
| `--border-accent` | `1px solid #00ff9f` | Active/focused element borders |
| `--border-dim` | `1px solid #161b22` | Subtle separators |
| `--radius-none` | `0` | Default — everything is sharp-cornered |
| `--radius-sm` | `2px` | Sole exception: small inline code badges only |

**Left-border highlight pattern:** Active nav items, section headings, and featured project cards use `border-left: 3px solid var(--color-green-primary)` with `padding-left: var(--space-4)`. Primary visual hierarchy signal.

---

## Glow / Shadow

No `box-shadow` for elevation. Glow only:

| Token | Value | Usage |
|---|---|---|
| `--glow-green` | `0 0 8px rgba(0,255,159,0.4)` | Focused inputs, active CTAs |
| `--glow-green-strong` | `0 0 16px rgba(0,255,159,0.6), 0 0 32px rgba(0,255,159,0.2)` | Hover on primary buttons |
| `--glow-cyan` | `0 0 8px rgba(0,212,255,0.35)` | Hover on links |
| `--glow-amber` | `0 0 8px rgba(255,179,0,0.5)` | Warning badges |

---

## Scanline Overlay

Pure CSS, zero JS, zero animation cost:
```css
body::before {
  content: '';
  position: fixed;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 2px,
    rgba(0,0,0,0.08) 2px,
    rgba(0,0,0,0.08) 4px
  );
  pointer-events: none;
  z-index: 9999;
  opacity: 0.6; /* 0.3 on mobile */
}
```

---

## Custom Cursor (Desktop Only)

- `cursor: none` on `<body>`
- A `<div id="cursor">` tracks mouse via `transform: translate()` with `will-change: transform`
- Renders as `12px × 20px` block in `--color-green-primary` with `animation: blink 1s step-end infinite`
- On mobile/touch: disabled entirely, system cursor restored

---

## Z-Index Stack

| Layer | Z-Index | Element |
|---|---|---|
| Canvas rain | 0 | `<canvas>` fixed background |
| Grid underlay | 1 | CSS `background-image` on `<body>` |
| Content panels | 10 | Section wrappers |
| Nav bar | 100 | Fixed bottom tmux bar |
| Modals | 1000 | Project detail modal + backdrop |
| Scanline overlay | 9999 | `body::before` pseudo-element |
| Custom cursor | 10000 | `#cursor` div |
