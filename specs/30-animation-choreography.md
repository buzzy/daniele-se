# Animation Choreography System

Animations are not decorations. Every motion has a narrative purpose: the terminal is *booting*, *processing*, *receiving*, *classifying*. Stillness is the default state. Motion is an event.

The system operates on three layers:
1. **Infrastructure layer** — scanlines, cursor blink, CRT flicker. Run continuously, never interrupted by content animations.
2. **Choreography layer** — entrance, exit, transitions. Director-controlled sequences.
3. **Interaction layer** — hover states, keypress feedback. Immediate, never slowly eased out.

---

## CSS Timing Vocabulary

```css
:root {
  --dur-instant:     50ms;
  --dur-snap:       100ms;
  --dur-fast:       150ms;
  --dur-default:    250ms;
  --dur-deliberate: 400ms;
  --dur-cinematic:  600ms;
  --dur-dramatic:   900ms;
  --dur-boot:      1200ms;

  /* Named easing curves */
  --ease-terminal-in:  cubic-bezier(0.0, 0.0, 0.2, 1.0);   /* decelerate: data arriving */
  --ease-terminal-out: cubic-bezier(0.4, 0.0, 1.0, 1.0);   /* accelerate: data leaving */
  --ease-spring:       cubic-bezier(0.34, 1.56, 0.64, 1.0); /* overshoot: mechanical snap */
  --ease-linear:       linear;                               /* scanlines, tickers */
  --ease-step-4:       steps(4, end);                       /* typewriter quantization */
  --ease-step-8:       steps(8, end);                       /* coarser digital feel */
}
```

---

## I. Site Boot Sequence (Runs Once Per Session)

This is a film opening, not a loading screen. `sessionStorage.getItem('booted')` gates it.

### Phase 0 — Blackout (t=0ms)
`body { opacity: 0; background: #000; }`. No flash of unstyled content.

### Phase 1 — CRT Power-On (t=0–300ms)
A single horizontal 1px white line at vertical center. `width` animates from `0%` to `100%` over 300ms using `--ease-terminal-in`. This is the phosphor tube warming up.

### Phase 2 — Vertical Expansion (t=300–600ms)
The line expands vertically: `scaleY` from `0` to `1` on the main viewport, 300ms, `--ease-terminal-out`. Content area reveals itself expanding outward from center. Scanline overlay fades in simultaneously, reaching full opacity by t=600ms.

### Phase 3 — Static Burst (t=600–650ms)
50ms of full-screen CSS noise (`--ease-step-4`). `opacity: 0.15`. The signal locking in.

### Phase 4 — Prompt Appears (t=650–1400ms)
`daniele@testa:~$` types in via `steps(15, end)` on `max-width` from `0` to `15ch` over 750ms. Cursor blinks throughout. **This is the moment the user understands the metaphor.**

### Phase 5 — Cursor Hold (t=1400–2000ms)
600ms of nothing. The cursor blinks. The user waits. This pause is mandatory — it creates anticipation and implies the system is processing.

### Phase 6 — Navigation Materialization (t=2000–2600ms)
Nav items appear with 100ms stagger. Each item: `opacity` 0→1, `translateX` -8px→0, `--dur-deliberate` (400ms), `--ease-terminal-in`. Items overlap: item 2 starts while item 1 is at 160ms of its 400ms animation — a flowing cascade, not a jump cut.

| Nav item | starts at |
|---|---|
| Item 1 | 2000ms |
| Item 2 | 2100ms |
| Item 3 | 2200ms |
| Item 4 | 2300ms |
| Item 5 | 2400ms |

### Phase 7 — Hero Content (t=2200–3200ms)
Begins while nav is still arriving (deliberate 200ms overlap).

| Element | starts at | duration | properties | easing |
|---|---|---|---|---|
| Name heading | 2200ms | 600ms (`--dur-cinematic`) | `opacity` 0→1, `translateY` 12px→0 | `--ease-terminal-in` |
| Tagline | 2500ms | 400ms (`--dur-deliberate`) | `opacity` 0→1, `translateY` 12px→0 | `--ease-terminal-in` |
| CTA / scroll hint | 2900ms | 250ms (`--dur-default`) | `opacity` only | `--ease-terminal-in` |

**Total boot sequence: ~3200ms to full content visible.**

On subsequent loads within the session: skip to Phase 6, compress entire sequence to 400ms.

---

## II. Scroll Entrance Choreography

**Trigger:** `IntersectionObserver` with `threshold: 0.15`, `rootMargin: '0px 0px -60px 0px'`. The -60px margin prevents premature firing on fast scrolls — elements must be 60px inside the viewport before triggering.

**Cardinal rule: the first element in any section to animate is always the section label. It anchors the hierarchy. Everything follows it.**

---

### Section: Skills (htop process monitor)

The table assembles like a process list populating in real time.

| t (relative) | Element | Properties | Duration | Easing |
|---|---|---|---|---|
| t+0ms | Table frame/border | `opacity` 0→1 | `--dur-snap` (100ms) | `--ease-linear` |
| t+100ms | Header row (PID\|NAME\|CPU%…) | `opacity` 0→1 | `--dur-fast` (150ms) | `--ease-linear` |
| t+200ms | Separator line | `scaleX` 0→1 from left | `--dur-default` (250ms) | `--ease-terminal-in` |
| t+300ms | Row 1 | `opacity` 0→1, `translateX` -4px→0 | `--dur-deliberate` (400ms) | `--ease-terminal-in` |
| t+380ms | Row 2 | same | same | same |
| t+460ms | Row 3 | same | same | same |
| +80ms each | Each subsequent row | same | same | same |

The 80ms stagger at 400ms duration creates 320ms overlap between consecutive rows. Earlier rows are already stable while later rows arrive — exactly how a live process monitor refreshes.

**CPU bar fill:** starts 200ms after its row entrance begins. `width` from `0` to actual value, 600ms (`--dur-cinematic`), `--ease-spring`. The spring overshoot simulates a physical gauge needle settling.

**Category headers between row groups:** appear 80ms before their first row.

---

### Section: Projects (Classified Dossier Cards)

Cards slide from slightly below — dossiers are stacked, not filed horizontally.

```
Within a row: 100ms stagger
Between rows: 200ms stagger
Formula: delay = (row × 200ms) + (col × 100ms)
```

| t (relative) | Element | Properties | Duration | Easing |
|---|---|---|---|---|
| t+0ms | Section label | `opacity` 0→1, `translateY` 6px→0 | `--dur-default` (250ms) | `--ease-terminal-in` |
| t+200ms | Card 1 (row 0, col 0) | `opacity` 0→1, `translateY` 20px→0 | `--dur-cinematic` (600ms) | `--ease-terminal-in` |
| t+300ms | Card 2 (row 0, col 1) | same | same | same |
| t+500ms | Card 3 (row 1, col 0) | same | same | same |
| t+600ms | Card 4 (row 1, col 1) | same | same | same |

The 200ms gap between rows reads as "a new page of the dossier."

**CLASSIFIED stamp flare:** 300ms into each card's entrance, stamp `opacity` pulses from 0.08 → 0.25 over 80ms, then decays back to 0.08 over 300ms. It looks like a redaction artifact catching light.

On mobile (single column): uniform 120ms stagger.

---

### Section: Bio (Unix Man Page)

Man pages are read, not watched. Animation is minimal and serves readability only.

| t (relative) | Element | Properties | Duration | Easing |
|---|---|---|---|---|
| t+0ms | Section label | standard | `--dur-default` | `--ease-terminal-in` |
| t+150ms | `NAME` sub-header | `opacity` only | `--dur-fast` (150ms) | `--ease-linear` |
| t+250ms | Name line | `steps(22, end)` on `max-width` | 990ms total | — |
| t+400ms | `SYNOPSIS` header | `opacity` only | `--dur-fast` | `--ease-linear` |
| t+500ms | Synopsis block | `opacity` 0→1, `translateY` 4px→0 | `--dur-deliberate` (400ms) | `--ease-terminal-in` |
| t+700ms | `DESCRIPTION` header | `opacity` only | `--dur-fast` | — |
| t+800ms | Paragraph 1 | `opacity` 0→1, `translateY` 4px→0 | `--dur-deliberate` | `--ease-terminal-in` |
| t+1000ms | Paragraph 2 | same | same | same |
| t+1200ms | Paragraph 3 | same | same | same |

Paragraphs use `translateY 4px` — smaller than cards' 20px because text is more fragile. Large translates on body copy look aggressive.

---

### Section: Contact / Terminal Input

This section IS the interaction. Its entrance signals the terminal is ready and waiting.

| t (relative) | Element | Effect |
|---|---|---|
| t+0ms | Section label | standard |
| t+200ms | Prompt line | `steps(16, end)` on `max-width`, 400ms, cursor solid (not blinking yet) |
| t+600ms | Cursor | Transitions from solid → blinking. This is the signal: input is open. |
| t+700ms | Input field border | `scaleX` 0→1 from left, 250ms (`--dur-default`), `--ease-terminal-in` |
| t+950ms | Helper text / command hints | `opacity` 0→1, 600ms (`--dur-cinematic`). Slow arrival — secondary information. |

---

## III. Exit Choreography

Exit animations are faster than entrances. Information leaving is less interesting than information arriving.

**General exit rule:**
```
opacity: 1→0
translateY: 0→ -6px
duration: --dur-fast (150ms)
easing: --ease-terminal-out
```

The -6px upward exit (content processed and gone) is asymmetric with the entrance (which had no translate on most text). Information arrives from slightly below; it exits slightly above.

**Skills table on exit:** rows exit in reverse order — last row first, same 80ms stagger. The table drains from bottom to top, like a process list clearing.

**Project cards on exit:** all exit simultaneously. No stagger. A closed file folder is closed — not one card at a time.

**Section labels:** exit 100ms after the last child of their section. They named the section and wait to confirm everything else is gone before departing.

---

## IV. Terminal State Transitions

### Command Execution (Enter pressed)

| t | Event |
|---|---|
| t+0ms | Cursor stops blinking. Becomes solid `█`. |
| t+50ms | Prompt line text `opacity` drops to 0.6 — submitted, now read-only. |
| t+100ms | Processing indicator appears: `opacity` 0→1, `--dur-snap`. Three-dot animation begins at `--dur-default` per state via `steps(1, end)`. Runs max 800ms. |
| t+900ms (or ready) | Output block: `opacity` 0→1, `translateY` 8px→0, `--dur-deliberate` (400ms), `--ease-terminal-in`. Output arrives from slightly below — it was computed below the fold. |
| t+1100ms | New prompt assembles: `steps(16, end)` over 400ms. Cursor blinks at completion. |

**Error response:** identical timing but output uses `translateX -4px→0` instead of translateY — a lateral jolt, not a vertical arrival. The horizontal entrance is the only visual distinction between success and error.

---

### Section Switch (Nav click or terminal `cd /skills`)

| t | Event |
|---|---|
| t+0ms | Current content: `opacity` 1→0, `translateY` 0→-8px, `--dur-fast` (150ms), `--ease-terminal-out`. All elements exit simultaneously — no stagger on nav exit. |
| t+100ms | Scanline overlay opacity jumps from 0.03 to 0.12 over 50ms. Holds 100ms. (Deliberate overlap with exit — screen is redrawing.) |
| t+150ms | Static burst: 50ms, `opacity: 0.08`. Shorter than boot — this is a context switch, not a power-on. |
| t+200ms | New section content begins entrance choreography. First element at exactly t+200ms. |
| t+300ms | Scanline overlay returns to 0.03 over 150ms, `--ease-terminal-in`. |

Total obscured time: 200ms. Below the threshold of feeling sluggish; above the threshold of feeling instantaneous.

---

### Modal Open (Dossier card expanded)

| t | Element | Properties |
|---|---|---|
| t+0ms | Backdrop | `opacity` 0→0.85, `--dur-deliberate` (400ms), `--ease-terminal-in`. Background content `opacity` → 0.3 simultaneously. (No blur — blur is too soft for this aesthetic.) |
| t+0ms | Modal container | `scaleX` 0.4→1, `scaleY` 0.4→1, `transform-origin: center`, `--dur-deliberate` (400ms), `--ease-spring`. Snaps open like a physical folder thrown onto a desk. |
| t+0ms | Modal border | Draws sequentially: top→right→bottom→left. Each side: `--dur-snap` (100ms), staggered 50ms each. Full border visible at t+150ms. |
| t+200ms | Modal header | Types in: `steps(20, end)` over 800ms. |
| t+350ms | Stamp/badge | `scale` 1→1.05→1 (single pulse), `--dur-default` (250ms), `--ease-spring`. The "stamp applied" animation. |
| t+500ms | Body paragraphs | `opacity` 0→1, `translateY` 4px→0, 150ms stagger per paragraph, `--dur-deliberate` (400ms). |
| t+600ms | Footer / action buttons | `opacity` 0→1, `--dur-fast` (150ms). No translate. |

### Modal Close

| t | Event |
|---|---|
| t+0ms | Modal content: `opacity` 1→0, `--dur-snap` (100ms). All simultaneous — the shell closes its contents before the shell closes. |
| t+80ms | Modal container: `scaleX/Y` 1→0.4, `--dur-fast` (150ms), `--ease-terminal-out`. No spring on close — snatched away, not placed. |
| t+80ms | Backdrop: `opacity` 0.85→0, `--dur-default` (250ms). |
| t+200ms | Background content: `opacity` 0.3→1, `--dur-default` (250ms). |

Open: ~1200ms total. Close: ~330ms total. The asymmetry is intentional: opening is an event, closing is a dismissal.

---

## V. Micro-Interaction Choreography

### Navigation Hover

```
Enter: underline scaleX 0→1 from left. --dur-fast (150ms). --ease-terminal-in.
       Text color: --dur-snap (100ms). --ease-linear. (Color changes are binary, not gradual.)
Exit:  underline scaleX 1→0 from right. --dur-snap (100ms). --ease-terminal-out.
```

The direction reversal — enters from left, exits to right — is the detail that separates designed from default.

### Keypress Cursor Flicker

```
keydown: cursor opacity 1→0. --dur-instant (50ms). steps(1, end).
keyup:   cursor opacity 0→1. --dur-instant (50ms). steps(1, end).
```

The cursor reacts to mechanical input. Typing feels physical.

### Skill Row Hover

```
Row background: → var(--color-selection). --dur-snap (100ms). --ease-linear.
CPU bar height: 4px → 6px. --dur-snap (100ms). --ease-spring. transform-origin: bottom.
PID value: opacity 1→0.5→1 over --dur-deliberate (400ms). steps(8, end). Fires once on hover entry.
```

### Project Card Hover (in grid, pre-click)

```
Card: translateY 0→ -3px. --dur-fast (150ms). --ease-spring.
      box-shadow intensifies. --dur-default (250ms). --ease-terminal-in.
CLASSIFIED stamp: opacity 0.08→0.18. --dur-default (250ms). --ease-terminal-in.
Border color: → var(--color-accent). --dur-fast (150ms). --ease-linear.
```

---

## VI. Stagger Systems Reference

| Context | Stagger increment | Duration per item | Properties | Max stagger cap |
|---|---|---|---|---|
| List items (command output, `<li>`) | 60ms | `--dur-deliberate` (400ms) | `opacity` 0→1, `translateX` -4px→0 | 8 items (480ms max) |
| Card grid | row×200ms + col×100ms | `--dur-cinematic` (600ms) | `opacity` 0→1, `translateY` 20px→0 | None |
| htop rows | 80ms | `--dur-deliberate` (400ms) | `opacity` 0→1, `translateX` -4px→0 | None |
| Nav items | 100ms | `--dur-deliberate` (400ms) | `opacity` 0→1, `translateX` -8px→0 | 5 items |
| Bio paragraphs | 200ms | `--dur-deliberate` (400ms) | `opacity` 0→1, `translateY` 4px→0 | None |

---

## VII. Deliberate Overlap Table

These overlaps are specified relationships, not accidents.

| Pair | A duration | B starts | Overlap |
|---|---|---|---|
| Boot: Nav begins while prompt holds | 750ms (prompt) | 2200ms (nav at 2000ms) | 200ms |
| Skills: Row n+1 during row n entrance | 400ms | 80ms after row n | 320ms |
| Section switch: New content during static burst | 50ms (static) | t+200ms (static at t+150ms) | 50ms |
| Modal: Border draw during scale | 400ms (scale) | Both at t+0ms | Full parallel |
| Modal: Header types after border | 150ms (border last side) | Header at t+200ms | Sequential, 50ms gap |
| Card entrance: Stamp flare during card | 600ms (card) | Flare at card t+300ms | 300ms into card |

---

## VIII. What Never Animates

These are load-bearing choices. Stillness here is design.

1. **Scanlines** — always present, never transition in or out. They are the texture of the medium.
2. **Cursor blink rate** — 530ms on/off always. Never speeds up to signal urgency. It is a clock, not an emotion.
3. **Terminal surface background color** — always `#0a0c0f`. Never fades or shifts as a transition mechanism.
4. **Font size** — nothing zooms. Scale is reserved for modals. Hierarchy is color and weight, not motion.
5. **Section labels after entrance** — once visible, they do not react to scroll, parallax, or hover. They are infrastructure.
6. **htop table frame after entrance** — the frame is static. Only the content inside updates.
7. **Error messages after lateral entrance** — static. They do not shake, pulse, or fade. They are facts.
8. **Body copy during reading** — no breathing, no opacity variation, no parallax. The user is reading.
9. **Boot sequence on back navigation** — `sessionStorage` guard ensures the terminal is already running on return.
10. **Paragraph text size** — never grows on hover. Never zooms. Reading is not a hover interaction.

---

## IX. Reduced Motion

`@media (prefers-reduced-motion: reduce)`:

- All `translateX`, `translateY`, `scaleX`, `scaleY`: set to `0`. No movement.
- All `opacity` transitions: preserved, capped at `--dur-fast` (150ms).
- Boot sequence: skip to Phase 6, run at 150ms total. No CRT power-on. No static burst.
- Typewriter effects: instant `opacity` 0→1. No `steps()`.
- Stagger systems: all delays halved. Structure remains, duration compressed.
- CPU bar spring: replace `--ease-spring` with `--ease-terminal-in`. No overshoot.
- Cursor blink rate: unchanged. Always.
