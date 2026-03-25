# Loading Experience & Performance

## Preloader — Cinematic BIOS Boot Sequence

### Concept
A dedicated preloader screen mimicking a system POST / BIOS boot. Plays on first visit; skipped on repeat visits.

### Timing
- **Minimum duration:** 2 seconds (even if assets load faster)
- **Maximum duration:** 4 seconds (extends with `[WAITING FOR MODULES...]` if load takes longer)
- **Repeat visits:** Skipped entirely (detected via `sessionStorage` flag) — site fades in within 300ms
- **Skip option:** `[SKIP]` label appears bottom-right after 1.5 seconds; keyboard: `Escape`

### Visual Design
- Black background, monospace font, green-on-black or amber-on-black text
- Strictly BIOS/boot-log style — **no matrix rain** (cliché)
- Lines print sequentially with variable realistic delays (not uniform — some fast, some "thinking")

### Boot Sequence Content (Example)
```
Daniele-BIOS v30.0.0  © 1994–2026 Daniele Testa
CPU: Full-Stack Processor @ 30.0 YRS  [OK]
Testing RAM... 32768MB OK
Detecting storage devices...  [OK]

[OK] Loading daniele-core.ko
[OK] Initializing skills.ko
[OK] Mounting /projects  read-write
[OK] Mounting /contact   read-write
[OK] Starting experience daemon...

LOGIN: daniele
```
Ends with `LOGIN: daniele` and a blinking cursor, then transitions.

### Transition
- **Not** a fade — a scan-line wipe top-to-bottom (50ms), then the hero section is revealed underneath
- Hero must be fully interactive before the wipe fires

### Technical Constraints
- Preloader JS + CSS: **under 10KB total**
- **No framework or animation library** — vanilla JS and CSS animations only
- During preloader: load hero HTML/CSS, fonts, JS bundle
- All other assets (images, non-critical): lazy-loaded after hero is interactive

---

## General Performance Requirements

| Metric | Target |
|---|---|
| Time to First Contentful Paint | < 1.5s on 4G |
| Time to Interactive (hero) | < 3s on 4G |
| Lighthouse Performance Score | > 85 |
| Bundle size (initial) | < 150KB gzipped |

## Animation Performance
- All animations must use `transform` and `opacity` only (no layout-thrashing properties)
- Scanline effect: CSS-only (no canvas unless necessary)
- Reduce-motion: all animations must respect `prefers-reduced-motion: reduce`
- Glitch effects: throttled, never constant — triggered on interaction or section entry only

## Asset Strategy
- Fonts: preloaded with `<link rel="preload">`; use `font-display: swap`
- Images (project screenshots): lazy-loaded with native `loading="lazy"`
- Icons: inline SVG preferred over icon fonts
- No heavy third-party scripts in the critical path
