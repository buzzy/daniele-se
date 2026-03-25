# Mobile & Responsive Design

## Philosophy
Desktop-first design. Mobile experience preserves the aesthetic but adapts interactions to touch-native patterns. No section is removed on mobile — all content remains accessible.

## Breakpoints
| Breakpoint | Width | Layout |
|---|---|---|
| Desktop | ≥ 1024px | Full experience, all interactions |
| Tablet | 768–1023px | 2-column grid, full terminal |
| Mobile | < 768px | 1-column, adapted interactions |
| Minimum | 320px | Supported minimum |

---

## Hero Terminal — Mobile Adaptation
- **Text input hidden** on mobile
- **Command chips displayed** as large tappable items styled as prompt suggestions, not buttons
- Each chip is prefixed with `>_` to maintain terminal metaphor (e.g. `>_ help`, `>_ ls projects`)
- Tapping a chip "types" the command with typewriter animation and executes it
- The terminal output area is full-width, scrollable
- The overall aesthetic is preserved — visitor sees the terminal respond, just via tap instead of keyboard

## Skills (htop Monitor) — Mobile
- Columns reduced: show NAME, UPTIME, STATUS only (CPU%/MEM% hidden or shown on row expand)
- Tap a row to expand and see full details
- Filter function-key buttons become a horizontal scrollable pill row
- Sorting via column header tap (tap again to reverse)

## Projects (Dossier Cards) — Mobile
- Single column
- Cards full-width with all information visible
- Hover glitch effects replaced by tap-and-hold effect
- Filter buttons become a horizontal scrollable pill row

## Navigation (tmux bar) — Mobile
- Bottom bar collapses section labels into icons + abbreviated labels
- Still always visible
- Uptime counter hidden on smallest screens to save space

## Contact Form — Mobile
- Fields stack vertically (already column layout)
- Virtual keyboard handled natively — form scrolls up on focus
- Submit button full-width

## About (Man Page) — Mobile
- Full-width, scrollable
- Hanging indent reduced for narrow screens
- Section headers remain uppercase bold

## Preloader — Mobile
- Same experience, but skip appears after 1 second (mobile users are more impatient)
- Reduced number of boot lines (8–10 instead of full sequence) to keep it snappy

## Animations — Mobile
- Heavy animations (glitch, scanlines, idle possession) are **disabled on mobile** by default
- Light animations (typewriter, cursor blink) are preserved
- Rationale: battery life, performance on lower-end devices, and mobile CPUs handle CSS compositing differently
