# More Easter Eggs — Advanced Discovery Layer

These 10 easter eggs are for the most curious, technically literate visitors. They require more effort to find than the original 7 — some take multiple steps, some require real terminal knowledge, some are only visible in specific tools. None are ever documented publicly.

---

## 8. HTTP Response Headers — The Résumé Nobody Asked For

**Trigger:** Any visitor who opens the Network tab in browser DevTools and inspects response headers on any request to the site.

**Output:** The Nginx server returns a set of custom non-standard headers on every response:
```
X-Powered-By: 30-years-of-coffee
X-Stack: everything
X-Last-Outage: never
X-Uptime: since-before-you-knew-what-linux-was
X-Favorite-Editor: none-of-your-business
X-Todo: sleep
X-Resume: https://[domain]/api/me
```

**Implementation:** Set in `nginx.conf` via `add_header` directives on the root `location` block.

**Why:** Every developer checks the network tab. This rewards that reflex immediately with something that can't be found any other way.

---

## 9. Terminal: `ssh daniele@[domain]` — Fake SSH Handshake

**Trigger:** Typing `ssh daniele@[domain]` (or variations like `ssh prod`, `ssh root@prod`) in the hero terminal.

**Output:**
```
Connecting to [domain]:22...
OpenSSH_9.0 negotiating...
Warning: Permanently added '[domain]' to the list of known hosts.
daniele@[domain]'s password:
```
Cursor blinks. Any input to the password field triggers:
```
Permission denied (publickey).
Hint: You didn't think I'd leave password auth on, did you?
```

**Why:** The publickey-only refusal is exactly what a hardened server returns. A sysadmin will laugh on sight.

---

## 10. CSS Source: Hidden Morse Code Comment

**Trigger:** Reading the main stylesheet source in DevTools or `view-source:`. A comment block buried between unrelated rules that looks like decorative divider noise is actually readable Morse code.

**Content:** The dots-and-dashes decode to a real personal message or quote Daniele actually lives by. The specific message is Daniele's to choose — something short, true, and personal.

**Implementation:** A CSS comment block of dots and dashes, formatted across multiple lines, placed between two unrelated rule blocks so it blends in as noise until examined closely.

**Why:** Requires three layers of effort — find the CSS, notice the comment, decode it. Genuinely hidden and genuinely personal.

---

## 11. `curl -I https://[domain]` — The Full Server Boast

**Trigger:** Running `curl -I https://[domain]` from a real terminal (not the on-site one).

**Output:** Beyond the custom headers from easter egg #8, two headers are especially notable:
```
Server: DanieleOS/1.0 (battle-hardened)
X-Handcrafted: yes, unlike your last sprint
```

**Implementation:** Nginx `server_tokens off` + custom `server_header` (via `nginx_more_headers` module or equivalent) to replace the default Nginx server string.

**Why:** `curl -I` is a developer reflex when investigating a site. `DanieleOS/1.0` instead of `nginx/1.25.3` is immediately memorable.

---

## 12. DevTools Console: The Boot Sequence

**Trigger:** Opening the browser's developer console on page load (detected via `window.onerror` overriding plus performance timing — if DevTools is open, certain timing thresholds are exceeded).

**Output:** A styled boot sequence prints with delays to the console, simulating a kernel boot log:
```
[    0.000000] DanieleOS kernel 5.4.0-daniele booting...
[    0.001337] Memory: 30 years of experience loaded
[    0.042000] Mounting /proc/coffee... done
[    0.100000] Loading module: legacy_support.ko
[    0.210000] WARNING: legacy_support.ko is 28 years old and still running
[    0.333000] Network: stubbornly-opinionated stack loaded
[    1.000000] All systems nominal. Welcome to the machine.
```
Followed by a plain-text line:
```
If you're reading this, you're exactly the kind of person I want to work with.
```

**Implementation:** `console.log` with `%c` styling for green-on-black monospace. Always printed on load — DevTools detection simply makes it more likely to be noticed. The console message fires regardless.

**Why:** The console is where developers live. The final line is direct and human — a sharp contrast with the machine aesthetic.

---

## 13. Time-Based: Night Owl Mode (02:00–04:00 Local Time)

**Trigger:** Loading the site between 02:00 and 04:00 in the visitor's local timezone (via `new Date()`).

**Output:**
- Terminal prompt changes to: `daniele@daniele.se [03:17] ~$ why are you awake`
- A line auto-types in the terminal (no interaction needed):
  ```
  > same reason I am. something broke in prod.
  ```
- The site's green accent color subtly shifts from `#00ff9f` toward amber-green `#aaff00` for the duration of the session — like a terminal running warm.

**Why:** Specific, personal, and only discoverable by the night-owl developers who share the 3am debugging session experience.

---

## 14. Terminal: `vim daniele.txt` — Vim Mode

**Trigger:** Typing `vim daniele.txt` (or `vi`, `vim .`, `vim README.md`) in the hero terminal.

**Output:** A convincing Vim interface opens inside the terminal widget. The file contains a short plaintext bio written as a commit message body (72-char wrapped lines). Status bar shows `-- NORMAL --`.

**Interaction states:**
- Typing `i` → status bar changes to `-- INSERT --`
- Typing `:q` → exits with no message
- Typing `:wq` → exits with:
  ```
  "daniele.txt" written. Nice try — you can't change the facts.
  ```
- Typing `:q!` → shows:
  ```
  E37: No write since last change (use ! to override)
  ```
  …and does nothing. Requires `:q!!` which outputs:
  ```
  Fine. Quitting without saving. Story of every rewrite project.
  ```

**Why:** The `E37` error and the extra `!` are exactly what Vim actually does. Only someone who has used Vim for real will try `:q!` and recognize the error. `:q!!` is the desperate extra `!` everyone has typed at least once.

---

## 15. `/api/visitors` — Hidden Visitor Counter Endpoint

**Trigger:** Requesting `GET /api/visitors` directly. Discoverable only by reading the console boot log (egg #12), which mentions it in a comment, or by guessing.

**Output:**
```json
{
  "count": 4821,
  "unit": "humans",
  "bots_rejected": 19053,
  "ratio": "the usual",
  "note": "each one who found this endpoint gets +1 respect",
  "you": "counted"
}
```

**Implementation:** A minimal counter backed by a simple flat file or in-memory store on the server. Increments only on non-bot requests to the main site. `bots_rejected` is a separate counter fed by Nginx rate limiting / honeypot hits.

**Why:** `"you": "counted"` is a moment of direct acknowledgment. The `bots_rejected` count demonstrates operational awareness. The endpoint itself is the reward.

---

## 16. Alt+F4 / Cmd+W — Last Words Before Exit

**Trigger:** Pressing Alt+F4 (Windows/Linux) or Cmd+W (Mac) while focused on the site tab. The tab still closes normally — this fires in the `beforeunload` window event in the ~300ms before closure.

**Output:** The entire terminal screen flashes red once and a single line appears:
```
KERNEL PANIC — not syncing: you left before reading the good part
```
Then the tab closes as normal.

**Why:** Only visible to people fast enough to catch it — or those who try it deliberately after hearing about it. It doesn't fight the browser; it just leaves a mark on the way out.

---

## 17. Multi-Step PGP Treasure Hunt

A four-step discovery chain. Each step reveals only the next step.

**Step 1 — HTML source comment:**
```html
<!-- to verify authenticity, check /security.txt -->
```

**Step 2 — `/security.txt`** (a real IETF-standard file):
Contains Daniele's actual PGP fingerprint, contact policy, and at the bottom — a non-standard field:
```
X-Challenge: decode the key's comment field
```

**Step 3 — PGP public key comment field:**
Fetching the actual public key from the keyserver listed in `security.txt` and reading the comment metadata field (which most people ignore) reveals:
```
echo "aGVsbG8sIGN1cmlvdXMgb25l" | base64 -d
```

**Step 4 — Decode the base64:**
Running that command outputs:
```
hello, curious one
```
Followed by a URL: `https://[domain]/hired`

**Step 5 — The `/hired` page:**
A hidden page (not linked anywhere) that displays:
> "You found it. Most people don't get this far. Let's talk."
> With only a direct `mailto:` link.

**Why:** Uses real, legitimate tooling (security.txt is a real standard, PGP is real infrastructure). A developer who completes this has demonstrated: they read source code, they know standards, they can use a terminal, and they're curious enough to follow a 4-step chain. That is exactly who is worth a direct conversation.

---

## Easter Egg Master Index (All 17)

| # | Trigger | Discovery Method |
|---|---|---|
| 1 | Konami code | Keyboard sequence |
| 2 | `man daniele` in terminal | Terminal command |
| 3 | `sudo` in terminal | Terminal command |
| 4 | htop uptime hover | Mouse interaction |
| 5 | HTML source → `curl /api/me` | Source code |
| 6 | 404 URL | Navigation |
| 7 | Idle 45s in terminal | Time-based |
| 8 | Network tab response headers | DevTools |
| 9 | `ssh` in terminal | Terminal command |
| 10 | CSS source Morse code | Source code |
| 11 | `curl -I` from real terminal | Real terminal |
| 12 | Browser console on load | DevTools |
| 13 | Visit at 02:00–04:00 local | Time-based |
| 14 | `vim` in terminal | Terminal command |
| 15 | `/api/visitors` endpoint | URL guessing / console hint |
| 16 | Alt+F4 / Cmd+W | Keyboard |
| 17 | PGP treasure hunt (4 steps) | Source → standards → crypto → URL |
