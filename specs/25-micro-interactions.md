# Micro-Interactions & Fine Details

25 specific touches that make the site feel like an extraordinary amount of work went into it. Each is fully described and ready to implement.

---

## 1. Cursor Morphs Per Context

The default custom cursor is a blinking terminal underscore `_`. It changes shape based on what's under it:

| Context | Cursor shape |
|---|---|
| Default / body | `_` (blinking, terminal green) |
| Hovering a link | `>_` |
| Hovering a button / clickable | `[_]` |
| Hovering a project card | ASCII crosshair `·+·` |
| Inside terminal input | Standard text beam, but green |
| Hovering disabled element | `_` (dim, no blink) |

Implemented as a custom cursor layer (`#cursor` div) tracking mouse at 60fps via `mousemove`.

---

## 2. Character Rain Reacts to Mouse Proximity

Canvas rain columns within a 120px radius of the cursor:
- Accelerate to 3× speed
- Shift from dim green to bright near-white
- Creating a "parting wake" effect as the mouse moves

The effect normalizes over 400ms easing when the mouse leaves the area. Disabled on mobile (no mouse).

---

## 3. Character Rain Tilts on Fast Scroll

Rapid scrolling causes the rain to tilt diagonally in the scroll direction — as if the page is physically moving through the matrix. Tilt angle is proportional to scroll velocity, max ~8°. Normalizes over 400ms easing when scroll stops.

---

## 4. BIOS Preloader with Real System Fingerprinting

The boot sequence detects and prints real values about the visitor's system:
```
Detected CPU: Apple M3 Pro (Macintosh)
Detected OS: macOS 14.4
Detected Resolution: 2560×1600 @ 32bpp
Detected Browser: Safari 17.4
```
These appear as genuine POST lines, making every visitor's boot unique. Detected via `navigator.userAgent`, `screen.width`/`height`, `screen.colorDepth`.

---

## 5. Time-of-Day Terminal Greeting

The hero terminal prompt and welcome banner change based on local time:

| Time range | Prompt addition | Welcome tone |
|---|---|---|
| 00:00–05:59 | `// you're up late` | `> same reason I am. something broke in prod.` |
| 06:00–11:59 | `// good morning` | Standard greeting |
| 12:00–17:59 | `// good afternoon` | Standard greeting |
| 18:00–21:59 | `// good evening` | Standard greeting |
| 22:00–23:59 | `// still hacking?` | `> respect. let's get to it.` |

The local time also appears in the shell prompt: `daniele@portfolio [14:22]:~$`

---

## 6. Typewriter with Realistic Typos

When the terminal types out commands autonomously (hero banner, idle possession), it occasionally:
- Pauses mid-word
- Types a wrong character (adjacent key on QWERTY)
- Waits 180ms (the "oh no" moment)
- Backspaces and corrects

Frequency: once per sequence, randomized position. Randomized per session so it never feels identical. Never occurs on user-triggered command output (only on "automated" typing).

---

## 7. Skill Bars with Spring Overshoot

htop skill bars animate from 0% to their target value using a spring physics curve:
- Overshoots by 4–6% beyond target
- Snaps back to final value
- Like a physical gauge needle settling
- Duration: 600ms per bar, staggered 30ms between rows
- Fill uses a scanline texture overlay (CSS repeating gradient) so it looks like a CRT readout

---

## 8. Keyboard Sound Design (Off by Default)

Two optional audio modes toggled via the tmux nav bar (`--sfx` flag icon):

**Mode 1 — Keyclick:**
- Mechanical keyboard click sound on every typed character in the terminal
- 3 randomized sample variants to avoid repetition
- Satisfying "thunk" on modal open/close

**Mode 2 — Ambient:**
- Soft CRT hum loop at low volume
- Increases slightly in intensity during active terminal interaction

Both modes are off by default. Preference stored in `localStorage`. No audio plays without explicit user opt-in.

---

## 9. Project Card "Declassify" Hover Animation

On card hover:
1. The `CLASSIFIED` or `DECLASSIFIED` stamp rotates from -15° to -5°
2. Stamp color fades from red to a lighter red-orange
3. A scan line sweeps top-to-bottom across the card at 300ms as if a document scanner is reading it
4. Card content (description, tags) fades from slightly dimmer to full brightness during the sweep

On mouse-out: instant snap back to resting state (no reverse animation).

---

## 10. Focus States with "Marching Ants"

Keyboard-focused elements get a custom focus ring — NOT a static browser outline. Instead:
- A dashed green rectangle (`border: 2px dashed #00ff9f`)
- The `stroke-dashoffset` animates continuously, making the dashes march around the element like a Photoshop marquee selection
- Animation: 1s linear infinite

This is visible only to keyboard users (`:focus-visible` selector). Mouse clicks do not show it.

---

## 11. Scrollbar as Progress Bar with Percentage Label

The custom scrollbar (right edge, 4px wide, terminal green):
- Has a floating label at the thumb showing current scroll percentage: `47%`
- Label appears only on scrollbar hover, fading in over 150ms
- Monospace font, `--text-xs` size, right-aligned next to thumb
- On mobile: standard scrollbar behavior, no custom label

---

## 12. Section Headings Type Themselves on Scroll

All `<h2>` section headings do NOT fade in — they type themselves character by character using the typewriter primitive when scrolling into view. The cursor `|` blinks at the end until the content below starts loading. Heading max `maxDurationMs: 400ms`.

This applies to: About, Skills, Projects, Contact section headers.

---

## 13. tmux Tab Transitions with Terminal Flicker

As the user scrolls between sections and the active tmux tab changes:
- A 1-frame blank flash fires on the switching tab (simulating terminal screen redraw)
- The new active tab appears with its green background
- Duration of blank: 16ms (one frame at 60fps) — subliminal but noticeable on close inspection

---

## 14. Contact Form Staged `sendmail` Output

On form submit, the fields are replaced line-by-line with a simulated sendmail session:
```
MAIL FROM: visitor@email.com
250 OK
RCPT TO: daniele@[domain]
250 OK
DATA
354 Start input, end with <CRLF>.<CRLF>
...
...
.
250 OK: Message queued as a3f1c2d
```
Each line appears with a 200ms stagger. On failure, an SMTP error code replaces the success:
```
550 5.1.1 User unknown
```

---

## 15. OS-Aware Ambient Comments

The terminal boot sequence includes a one-liner about the visitor's OS, typed in dim comment-colored text:

| Detected OS | Comment |
|---|---|
| Windows | `# nice try, but this runs better on linux` |
| macOS | `# decent machine. good taste.` |
| Linux | `# you're home.` |
| iOS / Android | `# mobile. brave choice.` |

Never called out explicitly — just quietly there in the boot sequence for anyone who reads it.

---

## 16. Ambient Terminal Idle State (Enhanced)

After 45 seconds of no mouse movement or scroll, the hero terminal enters a "screensaver" state:
- A new fake process appears to start: `[INFO] Running diagnostics...`
- Random log lines appear at 3–8 second intervals:
  - `[INFO] Memory pressure: nominal`
  - `[WARN] Caffeine levels: low`
  - `[INFO] Side project daemon: still running`
  - `[DEBUG] Patience threshold: 94.3% consumed`
- Any interaction kills it with `^C` and returns to the normal prompt

The log lines are randomly selected from a pool of ~20 options, never repeating in the same idle session.

---

## 17. Link Underline Sweeps Like a Terminal Cursor

When hovering a text link:
- A cursor-style block sweeps left-to-right under the text over 200ms
- Settles as a solid underline (terminal green)
- On mouse-out: sweeps back right-to-left and disappears over 150ms

Implemented via CSS `::after` pseudo-element with `transform: scaleX()` and `transform-origin: left`.

---

## 18. Glitch Fires on Specific Words

A JS scanner runs at page load and finds all text nodes containing:
- `error`, `segfault`, `null`, `undefined`, `NaN`, `panic`, `killed`

Each matching text fragment gets a CSS class that applies a 400ms glitch animation on page load:
- 2px horizontal RGB color split (`text-shadow`)
- Quick 2px vertical jitter
- Fires once, then stops

Visitors who notice feel like they caught something. The effect is subtle — just enough to register.

---

## 19. Project Card Loading Skeletons (Intentional Delay)

Even if `projects.json` loads instantly, a 600ms artificial delay is added before rendering cards. During this time, card slots are filled with ASCII block character placeholders:
```
▓▓▓▓▓▓▓▓▓▓▓▓▒▒▒▒▒▒▒░░░░░░
▓▓▓▓▓▓▒▒▒▒░░░░
▓▓▒▒░  ▓▓▒▒░  ▓▓▒▒░
```
A shimmer animation sweeps across the block characters (dark → light → dark, left to right). This makes the cards feel like they're being "loaded from classified storage" rather than just rendering instantly.

---

## 20. Day/Night Color Temperature Shift

Between 8pm and 6am, the entire site subtly shifts its green palette:
- `#00ff9f` (daytime cool green) → `#aaff00` (night amber-green)
- Transition is imperceptible mid-session but obvious if you visit at different times of day
- Mimics a CRT terminal that has warmed up over hours of continuous use
- Transition applied via a CSS custom property interpolation over 2 minutes

---

## 21. Konami Code Unlocks an Enhanced Terminal Session

The existing Konami code easter egg (egg #1) is expanded: after the `SYSTEM BREACH` animation fades, a full-screen fake terminal opens with its own command set:

| Command | Response |
|---|---|
| `ls` | Lists fake classified directories |
| `whoami` | `root (temporarily)` |
| `cat resume.txt` | Full plaintext resume |
| `sudo rm -rf /` | `nice try. this partition is write-protected.` |
| `hack the planet` | Rain goes full white for 3s + dial-up modem sound (if sfx enabled) |
| `exit` | Returns to normal site |

This Konami terminal is a "reward mode" for visitors who know the secret.

---

## 22. Viewport-Aware Rain Density

The canvas rain adapts to the viewport:
- Ultrawide (> 2560px): maximum column density
- Standard desktop (1024–2560px): default density
- Tablet (768–1024px): 70% density
- Mobile (< 768px): 50% density, trail length 8
- `prefers-reduced-motion: reduce`: rain frozen into a static field of faint characters — aesthetic preserved, no animation

---

## 23. Copy-to-Clipboard with OS-Specific Terminal Confirm

Clicking an email address or social link to copy it triggers an inline terminal one-liner next to the element:

| OS | Output |
|---|---|
| macOS | `$ pbcopy daniele@[domain]` then `copied.` |
| Windows | `$ clip daniele@[domain]` then `copied.` |
| Linux | `$ xclip -sel clip daniele@[domain]` then `copied.` |

The confirm line fades out after 2 seconds. The OS is detected via `navigator.platform` or `navigator.userAgent`.

---

## 24. Footer with Live `uptime` Output

The footer contains a line styled exactly like Linux `uptime` output, live-updating:

```
up 4 min, 1 user, load average: 0.12, 0.08, 0.05
```

- Uptime: calculated from `performance.timeOrigin` (actual time since page load)
- Load average: three slowly-drifting random numbers (0.01–0.99), updated every 5 seconds with a smooth interpolation
- Updates every 60 seconds

---

## 25. `security.txt` and `humans.txt` — Real Standard Files

Two legitimate web standard files are present at their canonical paths:

**`/security.txt`** (RFC 9116):
```
Contact: security@[domain]
Expires: 2027-01-01T00:00:00.000Z
Encryption: https://[domain]/pgp-key.txt
Preferred-Languages: en, it, sv
X-Challenge: decode the key's comment field
```

**`/humans.txt`** (humanstxt.org standard):
```
/* TEAM */
Developer & Designer: Daniele Testa
Location: Sweden

/* THANKS */
30 years of debugging, production incidents, and coffee.

/* SITE */
Last update: 2026
Language: JavaScript
Standards: HTML5, CSS3, WCAG 2.1 AA
Components: Vanilla JS, Vite, nginx, Linux

/* FOR DEVELOPERS */
curl -s https://[domain]/api/me
```

Both files are plain text, served with correct content types. Developers who check for these (which many do) find them populated with personality.
