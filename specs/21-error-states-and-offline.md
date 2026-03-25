# Error States, Offline Behavior & Edge Cases

## Failed JSON Data Fetches

All JSON files fetched at runtime by `src/utils/data.js`. Each fetch is wrapped in try/catch with retry and fallback.

**Retry policy:** On network error, a single automatic retry fires after 5 seconds. If the retry also fails, the fallback state below is applied permanently until the next page load.

### `projects.json` Failure
```
$ ls projects
Error: Cannot read directory /projects — Connection refused
Retrying in 30s... or refresh to try again.
```
- Card grid not rendered; filter buttons not rendered
- A "retry" link triggers a re-fetch without page reload

### `skills.json` Failure
- Panel header and column headers render normally
- Data rows replaced with:
```
Tasks: 0 total | uptime: — | load average: error
[WARN] Failed to load process list. /data/skills.json unreachable.
```
- Filter buttons rendered but disabled (`aria-disabled="true"`)

### `config.json` Failure
- `availabilityStatus` defaults to `"unavailable"` (safe — avoids false "open to work")
- `email`, `github`, `linkedin`: default to empty strings; contact block shows placeholder dashes
- Tmux bar availability badge: `● UNAVAILABLE` in amber
- No error shown to user — page remains functional

---

## Failed Project Screenshot Images

If `screenshotUrl` 404s or fails to load (`onerror`), replace with styled terminal placeholder `<div>`:
```
┌──────────────────────────────┐
│  [SCREENSHOT UNAVAILABLE]    │
│  /img/projects/<id>.png      │
│  File not found              │
└──────────────────────────────┘
```
Monospace font, `--color-green-muted` on `--color-surface` background — consistent with aesthetic.

---

## JavaScript Disabled

```html
<noscript>
  <div style="background:#0a0c0f;color:#00ff9f;font-family:monospace;padding:16px;text-align:center;">
    JavaScript is required for the interactive experience.
    You are viewing the static version of this site.
  </div>
</noscript>
```
- Inline `style` only (no external CSS dependency)
- Static HTML shell content remains fully readable below the notice

---

## Offline / Service Worker

**No service worker implemented.** This is a portfolio site — if the server is down, unavailability is acceptable. Aggressive `Cache-Control: immutable` on static assets ensures repeat visitors load from browser cache regardless.

---

## Unhandled JavaScript Errors

- A global `window.onerror` handler logs errors to console (never suppresses them)
- No error boundary UI is shown — individual component failures fall through to their fallback states
- The one exception: if `main.js` itself fails to parse/execute (catastrophic JS error), the static HTML shell is already in the DOM and remains visible — the site degrades gracefully to the no-JS experience

---

## Contact Form — Client-Side Error States

Beyond the terminal UI messages defined in `18-backend-api.md`, the following field-level validation errors must be shown inline (below the relevant field) in addition to the terminal output block:

| Error condition | Inline message |
|---|---|
| `from` empty | `> FROM: required field` |
| `from` invalid email | `> FROM: invalid address format` |
| `subject` too short | `> SUBJECT: minimum 3 characters` |
| `message` too short | `> MESSAGE: minimum 10 characters` |
| Any field empty on submit | The empty field(s) highlighted with amber border |

Field error messages use `role="alert"` so they are announced by screen readers. They appear in `--color-amber` text, monospace, `--text-sm` size.

---

## Performance Degradation Graceful States

| Scenario | Behavior |
|---|---|
| Slow network (JSON loads > 2s) | Sections render immediately with structural HTML; data populates when available — no spinner |
| Canvas API not supported | `background.js` catches the error; body background remains `#0a0c0f` with CSS grid only — no error |
| `requestAnimationFrame` not available | Typewriter falls back to synchronous `finish()` render |
| Font load failure | Fallback monospace chain: `Cascadia Code → Fira Code → Consolas → monospace` — layout preserved |
| WebP/AVIF not supported | `<picture>` elements provide PNG fallback for all project screenshots |
