# Typewriter Animation Primitive

## File: `src/utils/typewriter.js`

A single reusable module handles all typewriter (character-by-character text reveal) animations across the entire site. **No inline `setTimeout` chains anywhere else.** All callers — terminal output, boot sequence, section headers, idle possession easter egg — use this module.

---

## API

```js
/**
 * Type text into a target element, character by character.
 *
 * @param {HTMLElement} target       - Element to type into (appends text nodes)
 * @param {string}      text         - Full text to type
 * @param {object}      [options]
 * @param {number}      [options.charsPerSecond=80]   - Characters/sec (default: ~12.5ms/char)
 * @param {number}      [options.maxDurationMs=1500]  - Hard cap; auto-speeds up if text exceeds it
 * @param {number}      [options.delayMs=0]           - Delay before first character
 * @param {boolean}     [options.newline=false]        - Append <br> after completion
 * @param {Function}    [options.onComplete]           - Callback fired on completion
 * @returns {TypewriterInstance}
 */
export function typewrite(target, text, options = {}) {}

/**
 * Type multiple strings sequentially.
 *
 * @param {Array<{target: HTMLElement, text: string, options?: object}>} items
 * @param {object}  [globalOptions]
 * @param {number}  [globalOptions.lineDelayMs=60]   - Pause between lines
 * @param {number}  [globalOptions.maxDurationMs]    - Total cap across all lines
 * @returns {TypewriterInstance}
 */
export function typewriteSequence(items, globalOptions = {}) {}
```

---

## TypewriterInstance

Both functions return:
```js
{
  cancel()   // Stop immediately; leave text at current render position; promise rejects
  finish()   // Skip to end; render full text synchronously; onComplete fires; promise resolves
  promise    // Promise — resolves on completion, rejects with {cancelled: true} on cancel()
}
```

---

## Timing Model

- **Default speed:** `80` characters per second (12.5ms/char) — looks natural, not slow
- **`maxDurationMs` enforcement:** If `text.length / charsPerSecond * 1000 > maxDurationMs`, `charsPerSecond` is automatically raised to fit within the cap. The cap is always honored.
- **Engine:** `requestAnimationFrame` (not `setInterval`). Each frame checks elapsed time and renders however many characters are due. Stays in sync with the browser paint cycle; avoids frame drops.

---

## `prefers-reduced-motion` Behavior

When `window.matchMedia('(prefers-reduced-motion: reduce)').matches` is `true`:
- `finish()` is called immediately upon invocation — full text rendered synchronously
- `onComplete` callback still fires
- `promise` resolves immediately
- **No code change required at the call site** — the module handles it internally

---

## Cursor Handling

The typewriter does **not** manage cursors. The blinking cursor is a CSS pseudo-element on the terminal input, controlled by the terminal module. Call sites manage their own cursor indicators around typewriter calls if needed.

---

## Cancellation Contract

When `cancel()` is called on an in-progress instance:
- The RAF loop stops immediately
- Text in `target` is left at its current rendered position
- `onComplete` does NOT fire
- `promise` rejects with `{ cancelled: true }`

Used by the idle terminal possession easter egg — on user interaction, `cancel()` is called on the sequence and the terminal resets.

---

## Concurrent Instances

No global lock. Concurrent instances are allowed and expected (the terminal runs a new instance per output line). Callers are responsible for sequencing when order matters — use `typewriteSequence` or `await instance.promise`.

---

## Usage Examples

```js
// Single output line in hero terminal
const tw = typewrite(outputEl, '[OK] Skills module loaded', {
  maxDurationMs: 800,
  onComplete: () => showPrompt(),
})

// BIOS boot sequence — multiple lines with delays between them
const seq = typewriteSequence([
  { target: line1, text: '[OK] Loading daniele-core.ko' },
  { target: line2, text: '[OK] Initializing skills.ko' },
  { target: line3, text: '[OK] Mounting /projects  read-write' },
  { target: line4, text: '[OK] Starting experience daemon...' },
], {
  lineDelayMs: 120,
  maxDurationMs: 3000,
})

// Skip to end immediately (e.g. user pressed Escape during preloader)
seq.finish()

// Cancel on user interaction (idle possession easter egg)
idleSeq.cancel()

// Await completion before showing the next UI element
await tw.promise
showCursor()
```

---

## Site-Wide Speed Reference

| Use case | `maxDurationMs` | Notes |
|---|---|---|
| Hero welcome banner | `1000` | First thing visitor sees — must be fast |
| Terminal command output (short) | `600` | Single line — instant feel |
| Terminal command output (long) | `1500` | Multi-line outputs like `man daniele` |
| BIOS boot sequence (full) | `3000` | Entire sequence, across all lines |
| Section header entry animation | `400` | Heading types in on scroll |
| Idle possession git log | `5000` | Long, slow — feels like a real session |
