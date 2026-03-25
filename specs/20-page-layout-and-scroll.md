# Page Layout, Scroll Architecture & Section Assembly

## Page Structure (HTML)

```html
<body>
  <canvas id="bg-canvas"></canvas>          <!-- Fixed, z-index 0 -->
  <div id="cursor"></div>                   <!-- Fixed, z-index 10000 -->
  <div id="preloader">…</div>               <!-- Removed from DOM after transition -->

  <a href="#main-content" class="skip-link">Skip to content</a>

  <main id="main-content">
    <section id="hero">…</section>
    <section id="about">…</section>
    <section id="skills">…</section>
    <section id="projects">…</section>
    <section id="contact">…</section>
  </main>

  <nav id="tmux-bar">…</nav>                <!-- Fixed bottom, z-index 100 -->
  <div id="modal-root"></div>               <!-- Project modal portal, z-index 1000 -->
  <div id="toast-root"></div>               <!-- Ephemeral notices, z-index 500 -->
</body>
```

---

## Section Order & Sizing

Sections stacked vertically in a single `<main>`. No client-side routing. URL does not change on scroll.

| Section | ID | Min Height | Notes |
|---|---|---|---|
| Hero | `#hero` | `100dvh` | Exactly one full viewport |
| About | `#about` | `80vh` | Man page; fits content |
| Skills | `#skills` | auto | htop panel is 60vh; section padding adds context |
| Projects | `#projects` | auto | Grid grows with card count |
| Contact | `#contact` | `60vh` | Form + contact block |

- Use `100dvh` (dynamic viewport height) — not `100vh` — to handle mobile browser chrome correctly
- `padding-bottom` on `<main>`: `64px` — prevents last section from hiding behind the tmux bar

---

## Scroll Behavior

- `scroll-behavior: smooth` on `<html>` **only** when `prefers-reduced-motion` is not `reduce`
- When `prefers-reduced-motion: reduce`: `scroll-behavior: auto`
- Programmatic scrolls use `element.scrollIntoView({ behavior: 'smooth', block: 'start' })`
- With `prefers-reduced-motion`: `element.scrollIntoView({ block: 'start' })` (no behavior)
- No scroll snap — sections are free-scrolled
- Scroll position is NOT restored on back-navigation (acceptable for this content type)

---

## Section Visual Separation

No dividers or borders between sections. Separation is achieved via:
1. **Section header** — styled as a terminal command line (e.g. `$ man daniele`):
   - `font-size: var(--text-lg)`, `color: var(--color-green-primary)`
   - `border-left: 3px solid var(--color-green-primary)`, `padding-left: var(--space-4)`
2. **Spacing** — `padding-top: var(--space-9)` (`96px`) on all sections except `#hero`
3. **Panel transparency** — section content panels use `rgba` surface color; ambient background visible behind them

---

## Section Entry Animations

Each section except `#hero` triggers on IntersectionObserver (threshold: 80% of top entering viewport):
- Section header types in (typewriter, max 400ms)
- Content panels: `opacity: 0 → 1` over 300ms
- Stagger delay: 50ms between header and content
- Fires once per page load — observation is unobserved after first trigger
- `prefers-reduced-motion: reduce` → call `typewriter.finish()` immediately; skip opacity animation

Two separate IntersectionObservers:
1. **Section entry observer** — triggers entry animations (threshold: 0.8, `rootMargin: '0px 0px -20% 0px'`)
2. **Nav active-tab observer** — tracks which section is in view (threshold: 0.5)

---

## Anchor Linking & Deep Links

- All five sections have matching IDs (`#hero`, `#about`, `#skills`, `#projects`, `#contact`)
- URL hash is **NOT** updated on scroll (`history.pushState` not used)
- Tab clicks do NOT update URL
- Deep-link support on page load: if `location.hash` is set, `document.getElementById(hash).scrollIntoView()` is called before IntersectionObserver initializes
- Browser back/forward: `popstate` listener in `nav.js` updates active tab to match new hash

---

## Static Assets — Icons & OG Image

### Favicon Design
Symbol: `>_` prompt glyph, `#00ff9f` on `#0a0c0f` background.

**Required files in `/public/`:**

| File | Size | Format |
|---|---|---|
| `favicon.ico` | 32×32 + 16×16 | ICO multi-size |
| `favicon.svg` | vector | SVG (dark-mode safe) |
| `apple-touch-icon.png` | 180×180 | PNG |
| `og-image.png` | 1200×630 | PNG (static, generated once) |

**OG image content:**
- Background: `#0a0c0f`
- Left: large `>_` glyph in `#00ff9f`
- Right: `Daniele Testa` (white, 36px bold) / `Full-Stack Engineer` (green, 22px) / `30 years · every stack` (gray, 18px)
- Scanline overlay at 40% opacity

**HTML `<head>` entries:**
```html
<link rel="icon" href="/favicon.ico" sizes="32x32">
<link rel="icon" href="/favicon.svg" type="image/svg+xml">
<link rel="apple-touch-icon" href="/apple-touch-icon.png">
```

---

## 404 Page Requirements

`404.html` is a fully standalone file:
- All CSS inlined (no external stylesheet)
- No JavaScript (kernel panic effect is pure CSS animation)
- Only external dependency: JetBrains Mono from Google Fonts (`<link rel="stylesheet">`)
- A `[REBOOT →]` link points to `/` (home)
- Nginx configured to serve this file for all 404 responses:
  ```nginx
  error_page 404 /404.html;
  ```
