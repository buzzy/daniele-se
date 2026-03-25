# Hero — Fullscreen Terminal Prompt

## Concept
The landing page IS a terminal. The entire first screen is a dark fullscreen terminal emulator. The visitor is dropped into an interactive shell and invited to explore.

## Entry Experience
- On load: a brief "boot sequence" plays (handled by the preloader — see `09-loading-performance.md`)
- Boot resolves into a clean terminal prompt: `daniele@portfolio:~$`
- A welcome banner types out at full speed (capped at 1 second total), giving the impression the user joined a session already in progress
- A blinking cursor awaits input
- Below the prompt: visible command chips / suggestions (clickable): `help` · `ls projects` · `cat about.txt` · `whoami` · `contact`
- Subtle hint: `Type a command or click one above to begin.`

---

## Available Commands

| Command | Output |
|---|---|
| `help` | Lists all available commands with one-line descriptions |
| `whoami` | Displays Daniele's name, title, years of experience |
| `cat about.txt` | Scrolls to / renders the About (man page) section |
| `ls projects` | Lists project names; scrolls to Project Showcase |
| `ls skills` | Scrolls to / renders the htop skills monitor |
| `cat contact.txt` | Displays contact details (email, GitHub, LinkedIn, availability) |
| `man daniele` | Scrolls to About section with pager effect; `q` returns to top |
| `clear` | Wipes all output from DOM, resets scroll, shows fresh prompt |
| `sudo <anything>` | Easter egg — prints sudoers refusal message |
| `exit` | Easter egg — refuses to exit with a quip |
| Unknown | `command not found: <input>` + `Type 'help' to see available commands.` |

---

## Skip Option
- `[SKIP →]` link persistent in top-right corner (keyboard: `Escape`)
- Empty Enter on blank input: no output, no new prompt line (mimics real bash)

---

## Keyboard & Input Behavior

### Command History
- Capped at 50 entries per session (sessionStorage only — does not persist across sessions)
- Arrow Up: cycle backward; Arrow Down: cycle forward; past newest → restores partial buffer
- Duplicate consecutive entries not stored; blank inputs not stored

### Tab Completion
- Exact single match: silent completion
- Ambiguous or no input: prints list of possible completions as output
- Does not intercept Ctrl+R (passes to browser)

### Ctrl+C
- With non-empty input (no selection): clears input, prints `^C` on new output line
- With empty input: prints `^C`, shows new blank prompt line

### Paste
- Multi-line paste: first line only, rest discarded silently
- Pasted content: trimmed of leading/trailing whitespace
- Paste does not auto-execute; user must press Enter
- Rich text stripped to plain text automatically

### Copy
- All output text is selectable via standard OS selection
- No global "copy output" button

---

## Output Behavior

### Scrolling
- Output area scrolls vertically; input line pinned to bottom
- After command execution: auto-scrolls to bottom (except long outputs — see below)
- Manual scroll up: suspends auto-scroll until next command is executed
- No horizontal scroll; all output wraps at terminal width

### Long Outputs (`ls skills`, `man daniele`)
- Rendered in full — no pagination or `--more` prompt
- Viewport scrolls to the **top** of the output block (not bottom)
- Typewriter animation per line, capped at 1.5 seconds total regardless of content length

### Output Rendering
- Plain text throughout
- URLs and links in `cat contact.txt` are real `<a>` tags opening in new tab
- Error output (unknown commands) uses dim red/amber color
- Output blocks separated from next prompt by one blank line
- Simulated processing delay: 80–200ms (randomized) before output appears

### Terminal Container
- Maximum height: 70vh; scrollable within that container
- Does not grow the page infinitely
- `clear` wipes all output from DOM (not just hidden), resets scroll, does not reset history

---

## Focus & Interaction

- Input auto-focused when terminal enters viewport (IntersectionObserver), not on page load
- Clicking terminal body (not on a link) refocuses the input
- CSS blinking cursor pauses when window loses focus (`animation-play-state: paused`)
- `:focus-visible` outline added for keyboard-only navigation users

---

## Resize Behavior

- Terminal container is fully fluid; reflows on resize — all content intact
- Mobile orientation change: chip tray re-renders; chips wrap if needed
- Input uses multi-line textarea or contenteditable (not `<input type="text">`) to handle wrap correctly

---

## ARIA & Accessibility

```html
<section role="region" aria-label="Interactive terminal">
  <div role="log" aria-live="polite" id="terminal-output">...</div>
  <textarea aria-label="Terminal command input" autocomplete="off" spellcheck="false"></textarea>
</section>
```
- Prompt prefix `daniele@portfolio:~$`: `aria-hidden="true"`
- Blinking cursor (CSS pseudo-element): `aria-hidden="true"`
- All output announced via `aria-live="polite"`
- Easter egg outputs also announced (no visual-only effects)
- Focus returned to input after every command execution

---

## Mobile Adaptation

- Text input hidden on mobile
- Command chips displayed as large tappable items styled as prompt suggestions
- Each chip prefixed with `>_` (e.g., `>_ help`, `>_ ls projects`)
- Tapping a chip executes the command with full typewriter animation
- Chips are ≥ 44×44px touch targets
- Tapping anywhere in terminal area (not a chip) focuses hidden input as fallback keyboard trigger
