# Visual Depth and Layering System

Every surface on the site is composed from multiple independent layers. No element has a single background color or single text shadow. The rule is: **always at least five layers**. The visitor cannot point to what makes it feel expensive — because no one layer is the answer.

---

## 1. The Five-Layer Background Stack

Never one background. Always five, composited independently:

```css
body {
  background-color: #0a0e0a;
  background-image:
    /* Layer 5: Hot pixel noise (topmost) */
    url("data:image/svg+xml,..."),        /* 200×200 feTurbulence SVG data URI */

    /* Layer 4: Horizontal scanline grid */
    repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      rgba(0,0,0,0.18) 2px,
      rgba(0,0,0,0.18) 4px
    ),

    /* Layer 3: Vertical CRT shadow mask columns */
    repeating-linear-gradient(
      90deg,
      transparent,
      transparent 3px,
      rgba(0,0,0,0.04) 3px,
      rgba(0,0,0,0.04) 4px
    ),

    /* Layer 2: Vignette radial */
    radial-gradient(ellipse 120% 100% at 50% 50%,
      transparent 40%,
      rgba(0,0,0,0.72) 100%
    ),

    /* Layer 1: Base phosphor warmth (center brighter, edges darker) */
    radial-gradient(ellipse 80% 60% at 50% 40%,
      #0f1f0f 0%,
      #070d07 100%
    );
}
```

The scanlines use `background-size: 100% 4px`. The noise tile uses a 200×200px SVG with `<feTurbulence>` rasterized to a data URI, tiled at native size. The vignette and phosphor warmth gradients interact: the center receives both warmth and reduced vignette, the corners receive full vignette and no warmth — creating a subtle lens-shaped bright zone that reads as a CRT screen's characteristic footprint.

---

## 2. Phosphor Glow That Breathes

Text glows are not static. The glow pulses on a 4-second sine wave, with two shadow radii animating independently so core and corona never move together:

```css
@keyframes phosphor-breathe {
  0%   { text-shadow:
    0 0 4px  rgba(0,255,65,0.90),
    0 0 12px rgba(0,255,65,0.45),
    0 0 28px rgba(0,255,65,0.18); }
  50%  { text-shadow:
    0 0 3px  rgba(0,255,65,1.00),
    0 0 18px rgba(0,255,65,0.60),
    0 0 45px rgba(0,255,65,0.22); }
  100% { text-shadow:
    0 0 4px  rgba(0,255,65,0.90),
    0 0 12px rgba(0,255,65,0.45),
    0 0 28px rgba(0,255,65,0.18); }
}
```

The inner shadow (4px) = electron beam core. The 12px layer = phosphor excitation halo. The 28–45px layer = atmospheric scatter. They animate at different intensities — not uniformly — so no pulse looks mechanical.

Applied to: terminal prompt, section headings (when visible), nav bar active tab, cursor.

---

## 3. SVG Filter Chain for CRT Curvature + Chromatic Aberration

A persistent SVG filter applied via `filter: url(#crt-warp)` on `.terminal-viewport`:

```xml
<filter id="crt-warp" x="-2%" y="-2%" width="104%" height="104%">
  <!-- Barrel distortion via displacement map -->
  <feTurbulence type="fractalNoise" baseFrequency="0.65" numOctaves="1"
    result="noise" seed="2"/>
  <feDisplacementMap in="SourceGraphic" in2="noise"
    scale="2.2" xChannelSelector="R" yChannelSelector="G"/>

  <!-- Chromatic aberration: split RGB channels -->
  <feColorMatrix type="matrix"
    values="1 0 0 0 0.003  0 0 0 0 0  0 0 0 0 0  0 0 0 1 0"
    result="r-channel"/>
  <feOffset in="r-channel" dx="-1.4" dy="0" result="r-shifted"/>

  <feColorMatrix in="SourceGraphic" type="matrix"
    values="0 0 0 0 0  0 1 0 0 0  0 0 0 0 0  0 0 0 1 0"
    result="g-channel"/>

  <feColorMatrix in="SourceGraphic" type="matrix"
    values="0 0 0 0 0  0 0 0 0 0  0 0 1 0 0  0 0 0 1 0"
    result="b-channel"/>
  <feOffset in="b-channel" dx="1.4" dy="0" result="b-shifted"/>

  <feBlend in="r-shifted" in2="g-channel" mode="screen" result="rg"/>
  <feBlend in="rg" in2="b-shifted" mode="screen"/>
</filter>
```

Red channel shifts -1.4px, blue +1.4px. At normal viewing scale the split is invisible. On large text or when the visitor looks closely, they suddenly notice it — that subliminal detail is what registers as crafted. The barrel distortion from `feDisplacementMap` (scale 2.2) is equally subtle — enough to curve straight lines by under 2px at the edges.

---

## 4. Three-Tier Text Rendering

Every primary text element has three coexistent rendering layers:

```css
.terminal-output p {
  /* Tier 1: The actual text */
  color: #00ff41;

  /* Tier 2: Three-radius phosphor glow */
  text-shadow:
    0 0 3px  #00ff41,
    0 0 10px rgba(0,220,40,0.50),
    0 0 22px rgba(0,180,30,0.25);
}

/* Tier 3: Phosphor persistence ghost — 1px-down offset clone */
.terminal-output p::after {
  content: attr(data-text);   /* JS sets data-text="same content" */
  position: absolute;
  top: 1px;
  left: 0;
  color: #00ff41;
  opacity: 0.06;
  filter: blur(0.4px);
  pointer-events: none;
  mix-blend-mode: screen;
}
```

The `::after` ghost is a phosphor persistence artifact — the image of the previous frame partially retained by the screen. At 6% opacity it is never consciously noticed but registers as texture depth. JS must set `data-text` on every line before it is typed.

---

## 5. Animated Canvas Grain Layer

A full-viewport `<canvas id="grain">` sits `position: fixed`, `z-index: 999`, `pointer-events: none`, `mix-blend-mode: soft-light`, `opacity: 0.038`.

```javascript
const grain = document.getElementById('grain');
const ctx   = grain.getContext('2d');

function renderGrain() {
  const img  = ctx.createImageData(grain.width, grain.height);
  const data = img.data;
  for (let i = 0; i < data.length; i += 4) {
    const v = Math.random() * 255 | 0;
    data[i] = data[i+1] = data[i+2] = v;
    data[i+3] = Math.random() < 0.5 ? 0 : (Math.random() * 18 | 0);
  }
  ctx.putImageData(img, 0, 0);
  requestAnimationFrame(renderGrain);
}
renderGrain();
```

60fps, never the same two frames. `mix-blend-mode: soft-light` adds texture to midtones while preserving deep blacks and bright glow cores — physically consistent with how film grain behaves on photographic light sources. The grain makes the glow feel real rather than applied.

**`prefers-reduced-motion`:** `renderGrain()` still runs but calls `cancelAnimationFrame` after the first frame — the grain is static rather than absent.

---

## 6. Terminal Bezel with Seven-Value Box-Shadow

The terminal viewport is not a flat rectangle. It has five distinct depth signals via seven shadow values:

```css
.terminal-viewport {
  border: 1px solid rgba(0,255,65,0.12);
  outline: 1px solid rgba(0,255,65,0.04);
  outline-offset: 3px;
  box-shadow:
    inset 0  0  40px rgba(0,0,0,0.80),   /* deep inner darkness */
    inset 0  0   8px rgba(0,0,0,0.90),   /* immediate inner shadow */
    inset 0  1px 0   rgba(0,255,65,0.08), /* top edge: light catch */
    inset 0 -1px 0   rgba(0,0,0,0.60),   /* bottom edge: shadow */
    0 0   60px rgba(0,255,65,0.07),      /* screen emanation glow */
    0 0  120px rgba(0,255,65,0.03),      /* distant atmospheric glow */
    0 8px 30px rgba(0,0,0,0.90);         /* physical object shadow on surface */
}
```

Reading the shadows: the screen sits inside a bezel (inset shadows), the bezel has physical thickness (asymmetric inset top/bottom), the monitor rests on a surface (0 8px 30px), and the screen emits light onto that surface (60px + 120px outer glow). These are physically consistent signals — when they cohere, the eye reads "object in space" rather than "flat rectangle."

---

## 7. Rolling Scanlines via Background-Position Animation

Scanlines roll continuously, simulating CRT refresh:

```css
@keyframes scanline-roll {
  from { background-position-y: 0px; }
  to   { background-position-y: -4px; }
}

.terminal-viewport::before {
  content: '';
  position: absolute;
  inset: 0;
  background-image: repeating-linear-gradient(
    0deg,
    transparent 0px,
    transparent 2px,
    rgba(0,0,0,0.11) 2px,
    rgba(0,0,0,0.11) 4px
  );
  background-size: 100% 4px;
  animation: scanline-roll 0.14s linear infinite;
  pointer-events: none;
  z-index: 2;
}
```

At 0.14s the roll is fast enough to be subliminal — not legible as animation, but perceived as the shimmer of a live CRT refresh. Below 0.08s: hypnotic noise. Above 0.3s: distracting. 0.14s is the exact threshold of subliminal.

---

## 8. htop Rows with Z-Depth Color Banding

Skill rows are not one color. Each row's luminance varies by position, simulating perspectival depth recession:

```javascript
rows.forEach((row, i) => {
  const depthFactor = 1 - (i / rows.length) * 0.4;
  // Alternating row tint
  row.style.setProperty('--row-alpha', 0.03 + (i % 2) * 0.025);
  // Luminance recession: top rows bright (near), bottom rows dimmer (far)
  const l = Math.round(92 * depthFactor);
  row.style.color = `hsl(130, 80%, ${l}%)`;
  row.style.background = `rgba(0,255,65,var(--row-alpha))`;
});
```

Top row: `hsl(130,80%,92%)` — bright, near.
Bottom row (~row 80): `hsl(130,80%,55%)` — dimmer, receding.

This is atmospheric perspective applied to a flat table. It costs three lines of JavaScript and produces a spatial illusion that makes the list feel like it has physical extent.

---

## 9. Cursor with Three Independent Behaviors

The blinking cursor has three behaviors composited simultaneously — never synchronized with each other:

```css
@keyframes cursor-blink        { 0%,49%{opacity:1}  50%,100%{opacity:0} }
@keyframes cursor-glow-pulse   { 0%,100%{box-shadow:0 0 4px #00ff41}
                                  50%   {box-shadow:0 0 12px #00ff41, 0 0 24px rgba(0,255,65,0.4)} }
@keyframes cursor-width-breathe{ 0%,100%{width:9px}  50%{width:10px} }

.cursor {
  display: inline-block;
  width: 9px;
  height: 1.15em;
  background: #00ff41;
  animation:
    cursor-blink         0.85s step-end    infinite,
    cursor-glow-pulse    1.7s  ease-in-out infinite,
    cursor-width-breathe 1.7s  ease-in-out infinite;
}
```

`step-end` for blink: authentic CRT behavior — no fade, hard cut. The glow and width pulse share a 1.7s cycle but are never in sync with the 0.85s blink cycle. Prime-number-adjacent periods ensure the three animations drift in and out of phase perpetually — the cursor is never in the same composite state twice.

---

## 10. Dossier Cards with Physical Depth Illusion

Project cards use six properties working together to simulate a thick printed card under a terminal screen:

```css
.dossier-card {
  background:
    radial-gradient(ellipse 160% 120% at 50% 0%,
      rgba(0,255,65,0.04) 0%, transparent 70%),
    #0b140b;

  border-top:    1px solid rgba(0,255,65,0.22);  /* near edge: light catch */
  border-left:   1px solid rgba(0,255,65,0.12);
  border-right:  1px solid rgba(0,255,65,0.05);
  border-bottom: 1px solid rgba(0,255,65,0.03);  /* far edge: in shadow */

  box-shadow:
    inset 0 1px 0 rgba(0,255,65,0.09),
    0  4px  8px rgba(0,0,0,0.70),
    0 12px 28px rgba(0,0,0,0.50),
    0  0   0 1px rgba(0,255,65,0.04);

  transform: perspective(800px) rotateX(0.4deg);
}
```

The `rotateX(0.4deg)` is imperceptible as rotation but changes how the border and shadow render — the top edge is fractionally closer, the bottom edge fractionally further. The asymmetric border intensities (0.22 top, 0.03 bottom) reinforce this physically. The near edge catches light; the far edge fades. This is physically consistent, and physical consistency is what reads as craftsmanship.

---

## 11. CLASSIFIED Stamp with Analog Degradation

The stamp must look aged, not clean CSS:

```css
.classified-stamp {
  color: rgba(255,30,30,0.82);
  font-family: 'Courier New', monospace;
  font-weight: 900;
  font-size: 1.4rem;
  letter-spacing: 0.35em;
  border: 3px solid rgba(255,30,30,0.75);
  padding: 2px 8px;
  transform: rotate(-14deg);
  filter: blur(0.3px) contrast(1.4) opacity(0.82);
  mix-blend-mode: hard-light;
  text-shadow:
    1px  1px 0 rgba(255,0,0,0.30),
   -0.5px 0  0 rgba(200,0,0,0.20);
}
```

`blur(0.3px)` + `contrast(1.4)` is the key: blur softens the edges, contrast re-sharpens selectively. The result is ink that has bled into paper fibers and dried — edges are simultaneously soft and high-contrast, exactly as real rubber stamps. The 14-degree rotation on a pixel grid creates sub-pixel rendering artifacts that add further organic irregularity at no extra cost.

---

## 12. Palette Z-Depth Luminance Layers

The color system has five distinct luminance tiers. Depth determines brightness and saturation — both decrease with depth, matching how human vision perceives dark/foggy environments:

| Layer | Role | Color | Luminance | Saturation |
|---|---|---|---|---|
| Z+2 | Interactive focus, cursor, active | `hsl(130,100%,62%)` | 62% | 100% |
| Z+1 | Primary text, command output | `hsl(130,90%,52%)` | 52% | 90% |
| Z0 | Secondary text, prompts, labels | `hsl(130,75%,38%)` | 38% | 75% |
| Z-1 | Borders, grid lines, dividers | `hsl(130,60%,22%)` | 22% | 60% |
| Z-2 | Background tints, pressed states | `hsl(130,45%,10%)` | 10% | 45% |

CSS custom properties:
```css
:root {
  --c-z2:  hsl(130,100%,62%);
  --c-z1:  hsl(130,90%,52%);
  --c-z0:  hsl(130,75%,38%);
  --c-z-1: hsl(130,60%,22%);
  --c-z-2: hsl(130,45%,10%);
}
```

Every design decision — which color goes on which element — is justified by the Z-layer of the element. Nothing is colored arbitrarily.

---

## 13. Per-Character Phosphor Variation During Typewriter

When text types out, each character appears with a small random luminance offset simulating tube variance, then settles:

```javascript
function appendChar(el, char) {
  const span = document.createElement('span');
  span.textContent = char;
  span.dataset.text = char;  // for ::after ghost
  // ±8% luminance variance per character
  const l = 50 + (Math.random() * 16 - 8);
  span.style.color = `hsl(130, 88%, ${l}%)`;
  // Brief overbrightness on arrival, then settle to steady state
  span.style.animation = 'char-settle 0.3s ease-out forwards';
  el.appendChild(span);
}
```

```css
@keyframes char-settle {
  0%   { filter: brightness(2.2);
         text-shadow: 0 0 8px #00ff41; }
  100% { filter: brightness(1.0);
         text-shadow: 0 0 3px rgba(0,255,65,0.7); }
}
```

The `char-settle` animation simulates the electron beam striking the phosphor: initial overexcitation (2.2× brightness), then decay to steady state. The per-character luminance variance means no two characters in a line are identical — exactly as on a real CRT tube.

---

## 14. Man Page Typographic Depth Layering

Four typographic states, each carrying a Z-depth signal:

```css
/* Section headers: Z+2, broadened tracking */
.man-section-header {
  color: var(--c-z2);
  font-weight: bold;
  letter-spacing: 0.05em;
  text-shadow: 0 0 6px rgba(0,255,65,0.8), 0 0 20px rgba(0,255,65,0.3);
}

/* Body text: Z+1 */
.man-body { color: var(--c-z1); }

/* Indented descriptions: Z0, with left rule as both indent and depth signal */
.man-indented {
  color: var(--c-z0);
  border-left: 1px solid var(--c-z-1);
  padding-left: 1.5ch;
}

/* Cross-references: Z0 + dashed underline */
.man-xref {
  color: var(--c-z0);
  text-decoration: underline dashed;
  text-decoration-color: rgba(0,255,65,0.30);
  text-underline-offset: 3px;
}
```

Every typographic distinction carries a Z-depth signal. The `text-underline-offset: 3px` lifts the underline clear of descenders — noticed only when absent.

---

## 15. Seven-Property Composite Hover with Staggered Durations

Nothing reacts to hover with a single property change. Card hover transitions seven properties, each with individually tuned durations so the interaction tells a physical story:

```css
.dossier-card {
  transition:
    transform          220ms cubic-bezier(0.23, 1, 0.32, 1),  /* lift */
    box-shadow         280ms cubic-bezier(0.23, 1, 0.32, 1),  /* shadow grows */
    border-top-color   150ms ease-out,   /* near edge catches light first */
    border-left-color  150ms ease-out,
    background-color   300ms ease,       /* warm glow arrives last */
    filter             200ms ease,
    outline-color      180ms ease;
}

.dossier-card:hover {
  transform: perspective(800px) rotateX(0.4deg) translateY(-3px) translateZ(4px);
  box-shadow:
    inset 0 1px 0 rgba(0,255,65,0.18),
    0  8px 16px rgba(0,0,0,0.80),
    0 20px 48px rgba(0,0,0,0.60),
    0  0   0 1px rgba(0,255,65,0.10),
    0  0  40px rgba(0,255,65,0.06);
  border-top-color: rgba(0,255,65,0.45);
  border-left-color: rgba(0,255,65,0.25);
  filter: brightness(1.08) contrast(1.04);
}
```

The staggered durations narrate the hover: border snaps at 150ms (edge catches light), transform and shadow arrive at 220-280ms (physical lift), background warmth finishes at 300ms (screen glow warms the surface). The sequencing tells a physically coherent story.

---

## 16. Grain Uses `mix-blend-mode: soft-light`, Not `overlay`

```css
#grain {
  mix-blend-mode: soft-light;
  opacity: 0.055;
}
```

`overlay` clips at extreme luminances. `soft-light` is gentler — it adds texture to midtones while preserving deep blacks and bright glow cores. Glowing text retains its luminous center untouched by grain while midtone background areas receive full texture. This is physically consistent with how film grain behaves on photographic light sources.

---

## Implementation Discipline

The depth investment is not in any single technique. It is in the refusal to leave any surface with fewer than five layers, any transition with fewer than three properties, any color unchosen. When a developer inspects the CSS and finds seven `box-shadow` values on a single element, all physically justified — that is what makes the site feel like it took enormous effort. Because it did.
