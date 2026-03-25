# SEO & Accessibility

## SEO Strategy — Static HTML Shell + JS Enhancement

### Approach
All key content is present in semantic HTML before JavaScript executes. JS enhances the display into the interactive terminal experience. Once JS loads, the shell is visually hidden. Search engine crawlers and screen readers see the raw HTML.

### Shell Content (in DOM, visible without JS)
- `<h1>Daniele Testa</h1>`
- `<section id="about">` — plain-text bio paragraph
- `<ul id="skills">` — every technology as a `<li>` element (core SEO keyword payload)
- `<section id="projects">` — each project as `<article>` with title, one-sentence description, tech keywords
- `<address id="contact">` — real `<a href="mailto:...">` and social `<a href>` links

### Head Meta Tags
```html
<meta name="description" content="Daniele Testa — full-stack developer with 30 years experience in ...">
<meta property="og:title" content="Daniele Testa — Developer Portfolio">
<meta property="og:description" content="...">
<meta property="og:image" content="/og-image.png">
<meta property="og:url" content="https://[domain]">
<meta name="twitter:card" content="summary_large_image">
```

### Structured Data (JSON-LD `Person` schema)
```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Daniele Testa",
  "url": "https://[domain]",
  "jobTitle": "Full-Stack Engineer",
  "knowsAbout": ["Python", "PostgreSQL", "Linux", "Docker", ...],
  "sameAs": ["https://github.com/...", "https://linkedin.com/in/..."]
}
```

### Technical SEO
- `robots.txt` — allow full crawl
- `sitemap.xml` — single-URL sitemap
- Canonical `<link rel="canonical">` pointing to root domain
- All assets served over HTTPS
- Core Web Vitals targets met (see performance spec)

---

## Accessibility — WCAG 2.1 Level AA

### Structural Requirements
- Landmark regions: `<main>`, `<nav>`, `<header>`, `<footer>`, `<section>`, `<article>`
- Logical heading hierarchy: `<h1>` (name) → `<h2>` (sections) → `<h3>` (subsections)
- Single descriptive `<title>` tag
- Skip-to-main-content link as first focusable element in document

### Keyboard Navigation
- All interactive elements reachable via Tab / Shift+Tab
- Terminal input: Tab escapes the input (no keyboard trap)
- Focus order follows logical document order
- Enter / Space activate buttons and links
- Arrow keys navigate sortable table columns in the htop skills section

### Focus Visibility
- All focusable elements show a visible focus ring
- On-brand focus style: `outline: 2px solid #00ff41` (terminal green) — 3:1 contrast compliant
- Never suppress outline without replacement

### Color Contrast
| Element | Required Ratio |
|---|---|
| Body text | ≥ 4.5:1 |
| Large text (18pt+) | ≥ 3:1 |
| UI components (borders, icons) | ≥ 3:1 |

- Status badges and skill indicators must include text labels, not just color

### Motion & Animation
- All animations disabled / simplified when `prefers-reduced-motion: reduce` is set
- No content flashing more than 3 times per second (WCAG 2.3.1)
- A global "Pause animations" toggle in the tmux nav bar (styled as `--no-motion` flag)
- Auto-playing animations longer than 5 seconds have a pause control (WCAG 2.2.2)

### Terminal Widget ARIA
```html
<section role="region" aria-label="Interactive terminal">
  <div aria-live="polite" id="terminal-output">...</div>
  <input aria-label="Terminal command input" autocomplete="off" spellcheck="false">
</section>
```
- Decorative terminal chrome (`>`, `$`, box-drawing characters): `aria-hidden="true"`
- Blinking cursor (CSS pseudo-element): `aria-hidden="true"`

### Forms — Contact Section
- Every `<input>` and `<textarea>` has an associated `<label>`
- Error messages: specific, identify the field, announced via `role="alert"`
- Required fields: `aria-required="true"` + visual indicator
- Success/failure state announced to screen readers

### Images & ASCII Art
- Meaningful images: descriptive `alt` text
- Decorative visuals (scanlines, CRT noise, ASCII borders): `alt=""` or CSS `background-image`
- ASCII art conveying content: `aria-label` or visually hidden text equivalent

### Touch Targets (Mobile)
- All tappable elements ≥ 44×44px
- All actions have a single-tap or keyboard equivalent (no gesture-only interactions)

### Text
- Body text minimum 16px
- Line height minimum 1.5 for body copy
- Reflows without horizontal scroll at 320px viewport (WCAG 1.4.10)
- Legible at 200% browser zoom
