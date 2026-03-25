# Visual Obsessions — CSS Detail Layer

Obsession-level CSS details that most visitors will never notice consciously but will feel as a sense of "this was made with extreme care."

---

## 1. Print Stylesheet

`@media print` styles ensure the site prints as a coherent document — useful for recruiters who print resumes.

```css
@media print {
  /* Force exact colors even on systems that strip backgrounds */
  * { -webkit-print-color-adjust: exact; print-color-adjust: exact; }

  /* Hide purely decorative elements */
  canvas, #cursor, #preloader, #tmux-bar, .project-card__stamp { display: none; }

  /* Each major section starts on a new page */
  section { page-break-before: always; }

  /* Project cards: 2-up grid layout for print */
  .projects-grid { grid-template-columns: 1fr 1fr; gap: 1.5rem; }
  .project-card { border: 1pt solid #000; padding: 0.75rem; }

  /* Man page prints at full legibility */
  .man-page { font-size: 10pt; line-height: 1.5; color: #000; }

  /* Restore link text (href visible after link text) */
  a[href]::after { content: " (" attr(href) ")"; font-size: 8pt; }
}
```

---

## 2. Badge / Status Pill CSS

**Active badge** (pulsing dot):
```css
.badge--active::before {
  content: '';
  display: inline-block;
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background: var(--color-green-primary);
  margin-right: 6px;
  animation: pulse-dot 2s ease-in-out infinite;
  box-shadow: 0 0 0 0 var(--color-green-primary);
}

@keyframes pulse-dot {
  0%   { box-shadow: 0 0 0 0 rgba(0, 255, 159, 0.6); }
  70%  { box-shadow: 0 0 0 6px rgba(0, 255, 159, 0); }
  100% { box-shadow: 0 0 0 0 rgba(0, 255, 159, 0); }
}
```

**Archived badge** (`//` prefix):
```css
.badge--archived::before {
  content: '// ';
  color: var(--color-comment);
  font-family: var(--font-mono);
}
```

**Tech tag** (`#` prefix):
```css
.tag--tech::before {
  content: '#';
  color: var(--color-green-dim);
  margin-right: 2px;
}
```

---

## 3. Terminal Output Text CSS

All `<pre>` blocks inside `.terminal-output`:

```css
.terminal-output pre {
  border-left: 3px solid #00ff41;
  padding-left: 1rem;
  margin: 0.5rem 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 1px,
    rgba(0, 255, 65, 0.015) 1px,
    rgba(0, 255, 65, 0.015) 2px
  );
  /* Scanline texture overlay — 2px pitch */
}

.terminal-output pre::after {
  content: '';
  position: absolute;
  inset: 0;
  pointer-events: none;
  background: repeating-linear-gradient(
    180deg,
    transparent 0px,
    transparent 1px,
    rgba(0, 0, 0, 0.04) 1px,
    rgba(0, 0, 0, 0.04) 2px
  );
}

/* Cursor at end of active typewriter output */
.terminal-output .typewriter-active::after {
  content: '█';
  color: var(--color-green-primary);
  animation: blink-cursor 0.6s step-end infinite;
}
```

---

## 4. Zoom Resilience (150% / 200%)

Rules ensuring layout survives browser zoom:

- **All font sizes:** `clamp()` with `rem` units only — never `px` for text
- **Layout widths:** `min(100%, 80rem)` pattern — never fixed pixel widths on containers
- **Hero terminal height:** `min(70vh, 600px)` — prevents terminal from consuming full screen at 200% zoom
- **Canvas rain:** Redraws via a `ResizeObserver` on `devicePixelRatio` change, recalculates column count
- **Viewport height units:** `100dvh` everywhere (not `100vh`) — accounts for mobile browser chrome
- **Custom scrollbar:** Hidden at zoom > 150% (`@media (resolution > 1.5dppx) and (hover: none)`) since zoom implies accessibility need

Test targets: Chrome 150%, Firefox 200%, Safari 150%.

---

## 5. Text Selection Styling (`::selection`)

```css
::selection {
  background: rgba(0, 255, 65, 0.25);
  color: var(--color-text-primary);
  text-shadow: 0 0 8px rgba(0, 255, 65, 0.6);
}

/* Terminal input selection: brighter, feels native */
.terminal-input::selection {
  background: rgba(0, 255, 159, 0.4);
}
```

The glow on `text-shadow` during selection makes selected text look like it's highlighted on a real CRT terminal.

---

## 6. Placeholder Text

All form input placeholders use `// ` comment-style prefix:

```css
input::placeholder,
textarea::placeholder {
  color: var(--color-comment);
  font-style: normal; /* Never italic — terminals don't do italic */
}
```

HTML placeholder values:
- Name field: `// e.g. Ada Lovelace`
- Email field: `// e.g. ada@domain.tld`
- Subject field: `// e.g. Let's build something`
- Message field: `// Your message here. Plain text preferred.`

---

## 7. Project Modal Backdrop

```css
.modal-backdrop {
  background: rgba(10, 12, 15, 0.85);
  backdrop-filter: blur(3px) brightness(0.7);
  /* Noise SVG overlay for film grain texture */
  background-image: url("data:image/svg+xml,..."); /* inline SVG noise pattern */
  background-size: 256px 256px;
}

.modal {
  animation: modal-rise 280ms var(--ease-terminal-in) both;
}

@keyframes modal-rise {
  from {
    opacity: 0;
    transform: translateY(24px) scale(0.97);
    filter: blur(2px);
  }
  to {
    opacity: 1;
    transform: translateY(0) scale(1);
    filter: blur(0);
  }
}
```

The `blur(2px)` in the animation start state gives a "focus pull" effect as the modal sharpens into view.

---

## 8. Animation Timing Curves

A complete named set of `cubic-bezier` easing functions as CSS custom properties:

```css
:root {
  /* Terminal: fast in, slight elastic overshoot */
  --ease-terminal-in:  cubic-bezier(0.16, 1, 0.3, 1);

  /* Retract: quick snappy exit */
  --ease-retract:      cubic-bezier(0.4, 0, 1, 1);

  /* Glitch: instant in, stepped irregular out */
  --ease-glitch:       steps(4, jump-end);

  /* Data load: linear with micro-pause at 50% */
  --ease-data:         cubic-bezier(0.4, 0, 0.2, 1);

  /* Modal open: spring overshoot */
  --ease-modal:        cubic-bezier(0.34, 1.56, 0.64, 1);

  /* Skill bar spring: aggressive overshoot */
  --ease-spring:       cubic-bezier(0.175, 0.885, 0.32, 1.275);

  /* Scan sweep: linear for CRT authenticity */
  --ease-scan:         linear;

  /* Hover lift: standard Material-like */
  --ease-hover:        cubic-bezier(0.2, 0, 0, 1);
}
```

All animations reference these variables — never hardcoded bezier values in component CSS.

---

## 9. Lazy-Loaded Image Placeholders

Project card images and screenshots use lazy loading with a styled placeholder while loading:

```css
.project-card__image[data-loading="true"] {
  background: var(--color-surface);
  position: relative;
  overflow: hidden;
}

/* Corner bracket ASCII art via CSS gradients */
.project-card__image[data-loading="true"]::before {
  content: '';
  position: absolute;
  inset: 8px;
  border: 1px solid transparent;
  background:
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) top left /16px 1px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) top left /1px 16px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) top right/16px 1px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) top right/1px 16px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) bottom left /16px 1px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) bottom left /1px 16px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) bottom right/16px 1px no-repeat,
    linear-gradient(var(--color-green-dim), var(--color-green-dim)) bottom right/1px 16px no-repeat;
}

/* Breathing animation on placeholder */
.project-card__image[data-loading="true"]::after {
  content: 'LOADING...';
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-family: var(--font-mono);
  font-size: var(--text-xs);
  color: var(--color-green-dim);
  animation: placeholder-breathe 2s ease-in-out infinite;
}

@keyframes placeholder-breathe {
  0%, 100% { opacity: 0.3; }
  50%       { opacity: 0.7; }
}
```

---

## 10. Horizontal Rule / Separator Elements

Three separator variants used across the site:

**`sep--section`** (between major content areas):
```css
.sep--section {
  position: relative;
  border: none;
  margin: 3rem 0;
}
.sep--section::before {
  content: '';
  display: block;
  height: 1px;
  background: linear-gradient(
    90deg,
    transparent 0%,
    var(--color-border) 15%,
    var(--color-border) 85%,
    transparent 100%
  );
}
.sep--section::after {
  content: attr(data-label);   /* e.g. data-label="// end section" */
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  padding: 0 1rem;
  background: var(--color-bg);
  font-family: var(--font-mono);
  font-size: var(--text-xs);
  color: var(--color-comment);
}
```

**`sep--card`** (inside project cards, between metadata and description):
```css
.sep--card {
  border: none;
  border-top: 1px dashed var(--color-border-dim);
  margin: 0.75rem 0;
  opacity: 0.5;
}
```

**`sep--eof`** (at document end, like a file EOF marker):
```css
.sep--eof::after {
  content: '~';
  display: block;
  color: var(--color-comment);
  font-family: var(--font-mono);
  font-size: var(--text-sm);
  text-align: center;
  padding: 2rem 0;
  /* Vim ~-lines at end of buffer */
}
```

---

## 11. Scrollbar Styling

```css
/* Webkit */
::-webkit-scrollbar {
  width: 4px;
}
::-webkit-scrollbar-track {
  background: transparent;
}
::-webkit-scrollbar-thumb {
  background: var(--color-green-dim);
  border-radius: 0; /* square, not rounded — terminal aesthetic */
}
::-webkit-scrollbar-thumb:hover {
  background: var(--color-green-primary);
}

/* Firefox */
* {
  scrollbar-width: thin;
  scrollbar-color: var(--color-green-dim) transparent;
}
```

The scrollbar percentage label (micro-interaction #11) is implemented as a separate absolutely-positioned element, not a CSS-only solution, to enable the dynamic percentage display.

---

## 12. Focus Outline Reset (Global)

```css
/* Remove browser default — replaced with marching ants (micro-interaction #10) */
*:focus {
  outline: none;
}

/* Show something for keyboard-only: handled by marching-ants.js */
*:focus-visible {
  outline: none; /* JS takes over */
}

/* But never remove focus from elements that need it for AT */
[role="button"]:focus-visible,
button:focus-visible,
a:focus-visible,
input:focus-visible,
textarea:focus-visible {
  /* marching-ants.js adds/removes .focus-visible class here */
}
```

---

## 13. Reduced Motion Fallbacks

```css
@media (prefers-reduced-motion: reduce) {
  /* Canvas rain: frozen static field */
  canvas#rain { animation: none; }

  /* All CSS animations: instant */
  *, *::before, *::after {
    animation-duration: 0.001ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.001ms !important;
  }

  /* Typewriter: JS reads this and calls finish() immediately */
  /* (detected via getComputedStyle on a sentinel element) */
  .motion-sentinel {
    transition-duration: 0.001ms;
  }
}
```

The `.motion-sentinel` element is a 1×1px hidden div. `typewriter.js` checks its computed `transition-duration` on init — if it's `0.001ms`, `prefers-reduced-motion` is active and all typewriters call `finish()` immediately.
