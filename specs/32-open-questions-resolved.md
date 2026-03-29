# Open Questions Resolved

This spec is the authoritative resolution of every gap, ambiguity, and cross-spec contradiction identified during a full audit of specs 01–31. When this spec conflicts with an earlier spec, this spec wins.

---

## A. UX and Interaction

### A.1 — Tab Completion: Single Match

The completed text replaces what the user typed in the input field, with the cursor positioned at the end. The command is **not** auto-executed — the user must press Enter. The user sees the full completed command in the input before deciding whether to run it.

### A.2 — Tab Completion: Multiple Matches

A single horizontal space-separated list appears on one line below the current input, matching standard bash behavior:

```
projects  skills  contact  about
```

Alphabetical order. No descriptions. No separator line. The input is preserved unchanged so the user can continue typing to narrow the match.

### A.3 — Multi-Line Paste Truncation

Silent discard. The first line is processed as typed. Subsequent lines are dropped with no feedback — no warning, no "⚠ paste truncated" message. This matches terminal convention.

### A.4 — Terminal Scroll Pinning Implementation

The terminal is a flex column container with `overflow-y: auto`. The output area is a growing `div` that fills available space. The input row uses `position: sticky; bottom: 0` inside that same flex column, with a background matching the terminal surface so it visually overlays scrolling output. No separate `position: fixed` or `position: absolute` element.

```css
.terminal-container {
  display: flex;
  flex-direction: column;
  overflow-y: auto;
  height: 100%;
}
.terminal-output { flex: 1 1 auto; }
.terminal-input-row {
  position: sticky;
  bottom: 0;
  background: var(--bg-surface);
  flex: 0 0 auto;
}
```

### A.5 — Contact Form Subject Pre-Fill

Pre-fill triggers from two sections only:

| Source section | Pre-filled subject |
|---|---|
| About (man page) | `Re: your background` |
| Skills (htop) | `Re: technical stack` |

No pre-fill from any other section. Projects, Contact, and Hero sections do not trigger pre-fill.

### A.6 — `[REDACTED]` Block Reveal Mechanic

**Click to reveal** (not hover). A single click triggers a horizontal scan-line wipe animation sweeping left-to-right over 30–40ms in amber/green, ending with the full text visible in the standard card text color. No re-redaction on second click. The text is permanently revealed for that page session.

Hover produces no effect on redacted blocks. This prevents accidental reveals while reading.

### A.7 — htop Initial Filter State

All categories shown simultaneously on page load. No default filter is active. The F1–F7 keys are visible but none is in a pressed/active state. The full 80-technology roster is displayed.

### A.8 — htop Panel Shrink on Filter

Smooth resize via CSS `max-height` transition:

```css
.htop-panel {
  max-height: 100%;
  transition: max-height 250ms ease;
}
```

When filter reduces list to ≤15 rows, `max-height` transitions down to `(15 × 24px) + header-height`. When filter is removed, it transitions back up. No snapping.

### A.9 — Browser Back Button with Modal Open

The browser back button does **not** close the modal and does **not** navigate. This is enforced via the History API: `history.pushState(modalState, '', window.location.href)` is called on modal open, and a `popstate` listener calls `history.pushState` again to re-push the state, effectively neutralising the back action. The modal stays open.

This is an intentional UX decision: the URL does not change when a modal opens (no deep-link URL for modal state), so back should not change what the user sees.

### A.10 — Default Active Nav Tab on Page Load

The hero/home tab (`~`) is the default active tab on initial page load, regardless of scroll position. It is the first tab in the nav bar and is highlighted from the moment the page loads.

### A.11 — Typewriter Typo: Frequency

**Once per full page session.** Across all typewriter sequences in a session, exactly one typo occurs in one randomly selected sequence. The session state is tracked in a module-level variable in `typewriter.js`. All other sequences type cleanly, without error.

### A.12 — `exit` Command Second Invocation Timeout

The second-exit detection window is **10 seconds**. After the first `exit` response, if no second `exit` is received within 10 seconds, the state resets and a subsequent `exit` is treated as a first invocation again.

---

## B. Content and Data

### B.1 — Domain Tag Taxonomy: Final List

The complete, authoritative domain tag list (no additions, no removals):

```
FINTECH | DEVOPS-TOOLING | E-COMMERCE | REAL-TIME |
INFRA | DATA | SYSTEMS | OPEN-SOURCE | CONSULTING | INTERNAL
```

Maximum **3 tags per project**. A fourth tag is a signal the project description needs tightening.

### B.2 — Featured Projects

Multiple projects can have `featured: true` simultaneously. The intent is **2–3 featured projects** maximum.

**Featured card visual treatment:**
- Spans full width (both columns) in the grid — `grid-column: 1 / -1`
- Terminal prompt prefix on the card title changes from `$` to `#`
- A blinking cursor appears at the end of the card title (always-on, not just on hover)
- Otherwise identical to non-featured cards

### B.3 — htop STATUS Field: Exact Thresholds

| Status | Criteria |
|---|---|
| `running` | Used professionally (paid work or serious open-source) **within the last 2 years** |
| `sleeping` | Used professionally **2–7 years ago** — retrievable with a day's warm-up |
| `stopped` | Used professionally **7–15 years ago** — requires deliberate relearning |
| `zombie` | Used professionally **more than 15 years ago**, or formally abandoned |

"Professionally" means paid employment, client work, or a significant open-source contribution. Personal hobby experiments do not count.

### B.4 — Version Range Data Source

Manual field in `skills.json`, filled in by Daniele. Field name: `versionRange` (string, free-form, e.g. `"v7.4 – v16"`). No inference from any other source. If left blank, the htop tooltip renders without a version line — no empty field rendered.

### B.5 — Man Page EXAMPLES Section

These are **placeholders for Daniele's real project names**. The spec does not invent fictional examples. In the source code, placeholders are marked:

```html
<!-- TODO: replace with real project before launch -->
```

The build script (`validate-data.js`) emits a **warning** (non-fatal) if any TODO placeholder is present in a production build. The site can ship with placeholders in development but the acceptance criteria (spec 17) require them resolved before launch.

### B.6 — PGP Key Setup (Easter Egg 17)

In scope but implementation is Daniele's responsibility.

**Canonical keyserver:** `keys.openpgp.org` — GDPR-compliant, appropriate for Sweden.

**`security.txt` Encryption field format:**
```
Encryption: https://keys.openpgp.org/vks/v1/by-fingerprint/<FINGERPRINT>
```

The easter egg treasure hunt (spec 23 egg #17) cannot be considered complete until the key is actually published. Key generation steps are out of scope for the spec. The `security.txt` file ships as a placeholder until the key is published.

### B.7 — `humans.txt` MIME Type

`text/plain; charset=utf-8`. Nginx must serve it with that explicit `Content-Type` header:

```nginx
location = /humans.txt {
  add_header Content-Type "text/plain; charset=utf-8";
}
```

### B.8 — CRT Hum Frequency

**50 Hz.** The site is hosted in Sweden; authenticity means matching the actual electrical environment. European mains is 50 Hz. The synthesized CRT hum must use:

- Fundamental: 50 Hz sine wave
- Second harmonic: 100 Hz (−12 dB relative to fundamental)
- Third harmonic: 150 Hz (−18 dB relative to fundamental)

This supersedes spec 27 which incorrectly specified 60 Hz (North American).

### B.9 — Domain Name

The canonical domain is **`daniele.se`**. All `[domain]` placeholders throughout the spec set are replaced with `daniele.se`.

### B.10 — Canonical Easter Egg Count

**17 is the canonical total.** Hidden terminal commands (spec 24) are a **separate category: "terminal secrets"** — discoverable mechanics requiring knowledge to trigger, not passive surprises.

The 20 hidden terminal commands from spec 24 overlap with eggs only where a command *triggers* an egg (e.g., `sudo`, `man daniele` are listed in spec 05's documented commands and also in spec 24). These overlaps should be resolved in favor of spec 05: documented commands are not hidden; they should be removed from spec 24's "hidden" list.

Revised spec 24 hidden commands: remove `man daniele`, `sudo`, `exit` (first invocation) — these are documented. The remaining ~17 are genuinely undocumented. The 17-egg total from spec 23 is authoritative.

### B.11 — Night Owl Mode Color Token

`#aaff00` is confirmed and added to the design token system as:

```css
:root {
  --color-night: #aaff00;  /* late-night amber-green: 8pm–6am color temperature shift */
}
```

This sits between the standard phosphor green and amber, intentionally acid to signal degraded late-night perception. It is used exclusively by the time-based color shift (spec 25 #20) and night owl mode (spec 23 egg #13).

### B.12 — CSS Morse Code Message (Easter Egg 10)

The message to encode in Morse is: **"Ship it. Then make it better."**

Morse encoding:
```
... .... .. .--. / .. - .-.-.- / - .... . -. / -- .- -.- . / .. - / -... . - - . .-.
```

This is the committed value. Daniele may override by editing the CSS comment before launch, but this ships if he does not.

**CSS comment format** (placed between two unrelated rule blocks to appear as decorative noise):

```css
/* ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· */
/* ... .... .. .--. */
/* .. - .-.-.- */
/* - .... . -. */
/* -- .- -.- . */
/* .. - */
/* -... . - - . .-. */
/* ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· ·-·· */
```

---

## C. Technical and Deployment

### C.1 — Contact Form Duplicate Guard

**Persist to a flat file**, not in-memory. Write the SHA-256 hash + timestamp as a JSON array to `/var/lib/daniele-se/contact-dedup.json` on every submission. On service startup, load and prune entries older than 10 minutes.

**Write pattern:** write to `.tmp`, then `rename()` — atomic, safe against crashes mid-write.

```javascript
// On startup
let dedup = [];
try {
  const raw = fs.readFileSync(DEDUP_FILE, 'utf8');
  dedup = JSON.parse(raw).filter(e => Date.now() - e.ts < 10 * 60 * 1000);
} catch {}

// On submit
dedup.push({ hash, ts: Date.now() });
fs.writeFileSync(DEDUP_FILE + '.tmp', JSON.stringify(dedup));
fs.renameSync(DEDUP_FILE + '.tmp', DEDUP_FILE);
```

### C.2 — Email Sending Implementation

**nodemailer with a real SMTP relay.** Local `sendmail` is unreliable for deliverability from a VPS in Sweden (SPF/DKIM alignment is harder to guarantee).

```javascript
const nodemailer = require('nodemailer');
const transporter = nodemailer.createTransport({
  host: 'smtp.fastmail.com',
  port: 465,
  secure: true,
  auth: {
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  }
});
```

Credentials in `.env` file, never committed to git. `.env` is in `.gitignore`. The deploy process copies `.env` to the server manually (or via a secrets manager).

Alternative: Mailgun free tier if a dedicated sending domain is preferred over Fastmail.

### C.3 — Complete Spam Keyword Blocklist

```javascript
const BLOCKLIST = [
  // Link injection
  'http://', 'https://', 'www.', '<a ', 'href=',
  // HTML injection
  '<script', '<iframe', '<img ', 'onclick=', 'onerror=', 'javascript:',
  // Adult spam
  'porn', 'xxx', 'onlyfans', 'escort',
  // Gambling
  'casino', 'poker', 'betting', 'slots',
  // Pharmacy
  'viagra', 'cialis', 'pharmacy', 'pills', 'medication',
  // SEO/crypto spam
  'seo service', 'buy followers', 'crypto'
];
```

24 entries total. Applied case-insensitively to the combined subject + message text.

### C.4 — Rate Limiting: Both Layers, Different Roles

Both Nginx and the backend implement rate limiting — they are not redundant; they defend different surfaces.

**Nginx (primary — handles all traffic including bots):**
```nginx
limit_req_zone $binary_remote_addr zone=contact:10m rate=5r/m;
location /api/contact {
  limit_req zone=contact burst=2 nodelay;
  proxy_pass http://127.0.0.1:3001;
}
```

**Backend (`express-rate-limit`, secondary — catches direct port access in dev/misconfigured proxy):**
```javascript
const rateLimit = require('express-rate-limit');
app.use('/api/contact', rateLimit({
  windowMs: 15 * 60 * 1000,  // 15 minutes
  max: 5,
  standardHeaders: true,
  legacyHeaders: false
}));
```

### C.5 — Custom `Server:` Header (nginx-extras)

`nginx_more_headers` (`ngx_headers_more`) is **not in the standard Nginx package**. Install `nginx-extras`:

```bash
apt install nginx-extras
```

Then in `nginx.conf`:
```nginx
more_set_headers "Server: DANIELE-OS/1.0 (battle-hardened)";
server_tokens off;
```

If `nginx-extras` is unavailable, `add_header Server "..."` does not override the built-in `Server:` header — only `more_set_headers` does. `nginx-extras` is the required solution.

### C.6 — `dvh` Unit Fallback

```css
min-height: 100vh;    /* fallback for browsers without dvh support */
min-height: 100dvh;   /* overrides on supporting browsers (Chrome 108+, Safari 15.4+) */
```

No additional calculation needed. `100vh` is visually acceptable as a fallback — the only difference is mobile browsers with dynamic toolbars, which is a cosmetic issue on an edge case, not a functional regression.

### C.7 — Build Script Chain (Validation Before Build)

Validation is wired as a `prebuild` npm hook so it runs automatically:

```json
{
  "scripts": {
    "validate-data":  "node scripts/validate-data.js",
    "prebuild":       "npm run validate-data",
    "build":          "vite build",
    "dev":            "vite",
    "preview":        "vite preview"
  }
}
```

Validation failure exits non-zero and aborts the build. Manual validation with `npm run validate-data` remains available for development without triggering a full build.

### C.8 — Backend Service Architecture

**Two separate Node.js processes on two ports:**

| Service | File | Port | systemd unit |
|---|---|---|---|
| Contact form | `api/contact/server.js` | `3001` | `daniele-contact.service` |
| /api/me | `api/me/server.js` | `3002` | `daniele-me.service` |

Nginx routes by path prefix:
```nginx
location /api/contact { proxy_pass http://127.0.0.1:3001; }
location /api/me      { proxy_pass http://127.0.0.1:3002; }
```

Keeping them separate means the contact form can restart on deploy without affecting the `/api/me` easter egg endpoint.

### C.9 — JSON Schema Version Field

All three JSON data files include `"_schemaVersion": "1"` at the top level (string, not integer — easier to extend to `"1.1"` without breaking equality checks).

```json
{
  "_schemaVersion": "1",
  "projects": [ ... ]
}
```

`validate-data.js` checks this field and rejects unknown versions with a non-zero exit.

### C.10 — `lastUpdated` in `config.json`

**Manually set by Daniele** in ISO 8601 format (`"2026-03-26"`). This is a deliberate editorial field — it reflects when content was meaningfully updated, not when a config file was touched. A git hook would produce noise. The deploy script does not touch it.

### C.11 — Lighthouse Target: Authoritative Values

**Spec 17 is authoritative:** `≥90 desktop / ≥80 mobile`.

Spec 09's single value of `>85` is superseded. The split values are more precise: the terminal aesthetic is primarily a desktop experience; desktop gets the higher target. Mobile gets `≥80` because the animated CRT effects have a measurable cost on low-end phones.

### C.12 — DevTools Console Boot Sequence: Always Print

**Drop the detection. Print unconditionally on every page load.**

The detection approach (`window.onerror` + performance timing) is unreliable across browsers and versions. A console sequence that fires 60% of the time is a broken easter egg. One that fires 100% is a feature.

Print on `DOMContentLoaded`. Developers see it. Casual users who never open DevTools never see it. The discovery moment is intact without brittle detection.

```javascript
document.addEventListener('DOMContentLoaded', () => {
  printConsoleBootSequence();  // always, unconditionally
});
```

---

## D. Cross-Spec Contradictions (Resolved)

### D.1 — Focus Ring: Marching Ants Win

**Marching ants are the intended implementation.** Spec 11's `outline: 2px solid #00ff41` was a baseline fallback-level requirement, not the ceiling. Spec 25's animated dashed ring (spec 25 #10) is the design intent for a site of this caliber.

**Graceful degradation with no-JS fallback:**

```css
/* Base CSS fallback — always present, no JS required */
:focus-visible {
  outline: 2px solid #00ff9f;
  outline-offset: 2px;
}

/* JS adds [data-focus-enhanced] to <html> after marching ants initialize.
   Elements that have the animated ring then suppress the static outline. */
[data-focus-enhanced] .focus-enhanced:focus-visible {
  outline: none;  /* marching ants JS ring takes over */
}
```

JS adds `data-focus-enhanced` to `<html>` on initialization. Without JS: every focused element shows the static `2px solid #00ff9f` outline — WCAG 2.1 AA compliant, on-brand, zero dependencies. Never no focus ring under any conditions.

### D.2 — Email Obfuscation vs. Terminal `<a>` Tags

**Two-layer rule — no contradiction when correctly separated:**

- **SEO / noscript layer** (`<address id="contact">`): real `<a href="mailto:daniele@daniele.se">` tag — visible only to crawlers and screen readers, visually hidden once JS loads.
- **Terminal UI layer** (`cat contact.txt` output): email displayed as styled plain text (`daniele@daniele.se`), rendered as a `<span>` with click-to-copy behavior (micro-interaction #23), visually underlined and terminal-green, but **no `href` attribute in the DOM**.

The terminal output never emits a live `mailto:` link. "No plaintext email in page source" applies to the static HTML and terminal output. The SEO layer is the exception because it's structurally necessary for accessibility.

### D.3 — Color Token Naming and Primary Green

**Canonical primary green: `#00ff9f`** (more cyan-leaning, from spec 15).
**Naming convention: `--color-*` prefix** (spec 15 pattern).

Spec 31's `--green-base: #39ff14` (more yellow-green) is a secondary, high-contrast variant used for bar fills, success text, and border-focus states. It is not the universal primary.

Role assignments:
- `--color-green-primary: #00ff9f` — active nav tab, cursor blink, leading rain character, marching ants ring, CTAs
- `--color-green-bright: #39ff14` — CPU bar fills, form success state, `text-shadow` glow cores, `border-focus`

Spec 31's CSS block uses its own unprefixed names internally; whenever those are applied site-wide, they map to the `--color-*` tokens from spec 15. **Spec 15 is the single source of truth for all color tokens.**

### D.4 — Skill Bar Spring Overshoot (Confirmed Consistent)

No contradiction. `cubic-bezier(0.34, 1.56, 0.64, 1.0)` (spec 30's `--ease-spring`) produces approximately 5–8% overshoot, which fully satisfies spec 25's stated 4–6%. All three specs are consistent: spec 25 defines the behavior, spec 30 defines the easing function, spec 31 references `--ease-spring` for CPU bars. The correct bezier is `(0.34, 1.56, 0.64, 1.0)` — not `(0.175, 0.885, 0.32, 1.275)` which was a different variant mentioned in spec 28. Use `(0.34, 1.56, 0.64, 1.0)` everywhere.

### D.5 — Featured Project Visual Treatment

Featured cards render **above the grid at full width**, before the filter buttons take effect. Treatment:

- `grid-column: 1 / -1` — spans full width
- Label above the card: `// PRIORITY-1 · FEATURED OPERATION · CLASSIFICATION: PUBLIC`
- `border-left: 3px solid var(--color-green-primary)` with `padding-left: var(--space-4)`
- Terminal prompt prefix changes from `$` to `#` on the card title
- Blinking cursor at the end of the card title (persistent, not just hover)
- Shows an extra stat or notable quote not visible on grid cards
- **No additional stamp alongside CLASSIFIED** — the `PRIORITY-1` label IS the featured marker. Two stamps creates clutter.

### D.6 — Konami Code Sequence and Terminal Timing

The sequence is additive and sequential:

1. Konami code entered → site glitch/scanline effect fires
2. `SYSTEM BREACH DETECTED` alert displays with spoof readout
3. Breach animation plays for its full duration
4. Breach animation fades out
5. **500ms silence** (intentional beat — system taking control)
6. Full-screen Konami terminal opens

The 500ms gap after the breach animation makes the terminal appear as if a system is seizing control, rather than an instant visual transition.

### D.7 — Static HTML Shell: What Is Present Without JS

**Present and visible without JS:**
- `<h1>Daniele Testa</h1>` with title and years of experience
- `<section id="about">` — plain-text bio paragraph (no man page formatting)
- `<ul id="skills">` — each technology as a `<li>` element
- `<section id="projects">` — each project as `<article>` with title, one-sentence description, tech tags
- `<address id="contact">` — real `<a href="mailto:daniele@daniele.se">` and social `<a>` links

**NOT in the noscript layer:**
- Terminal hero UI, typewriter effects, boot sequence
- htop table formatting, interactive commands
- Marching ants focus rings
- `cat contact.txt` terminal output block
- Any JS-dependent animations or interactions

A `<noscript>` block displays: `"This site requires JavaScript for the interactive terminal experience."` The noscript layer is semantic HTML optimized for crawlers and screen readers, not users.

### D.8 — Custom Scrollbar on Mobile: Correct Media Query

**Use `@media (pointer: coarse)`**, not a viewport width breakpoint.

A tablet at 1024px wide still has a coarse pointer and should use standard scrollbar behavior. A desktop with a narrow browser window still has a fine pointer and should show the custom scrollbar. Pointer type is the correct semantic test for "touch device without a mouse."

```css
@media (pointer: coarse) {
  ::-webkit-scrollbar { display: none; }
  * { scrollbar-width: none; }
}
```

### D.9 — Boot Sequence Content Authority

**Spec 26 is authoritative for all boot sequence copy.** Spec 09 is authoritative for timing, skip behavior, transition mechanics, and performance constraints. When they conflict on words: spec 26 wins. When they conflict on behavior/timing: spec 09 wins. The two specs are now complementary, not competing.

### D.10 — Marching Ants Fallback (Same as D.1)

See D.1. The two-tier pattern (`data-focus-enhanced` attribute) ensures WCAG 2.1 AA compliance under all conditions: JS loaded → marching ants. JS not loaded → static green outline. Never zero focus indicator.
