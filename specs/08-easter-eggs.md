# Easter Eggs & Personality Moments

## Overview
Hidden features and personality moments that reward curious visitors and reinforce Daniele's 30-year developer identity. All should feel native to the terminal aesthetic — never forced or gimmicky.

---

## 1. Konami Code — System Breach
- **Trigger:** ↑↑↓↓←→←→BA anywhere on the page
- **Output:** Site glitches with heavy scanlines and static for 2 seconds, then a fake "SYSTEM BREACH DETECTED" alert fires. A spoof terminal readout appears: `root@daniele:~# sudo rm -rf /your-assumptions`. Fades back to normal after 4 seconds.
- **Why:** Every developer who was around in the 90s knows this code. Instant recognition.

---

## 2. `man daniele` in the Hero Terminal
- **Trigger:** Typing `man daniele` in the hero terminal prompt
- **Output:** Smooth scroll to the About section. Renders with a pager effect — `(END)` shown at the bottom, pressing `q` returns to the top.
- **Why:** Closes the loop on the man page metaphor — the command actually works.

---

## 3. `sudo` Anything
- **Trigger:** Any command starting with `sudo` in the hero terminal
- **Output:**
  ```
  [sudo] password for daniele:
  ●●●●●●●
  daniele is not in the sudoers file. This incident will be reported.
  ```
- **Why:** One of the most recognized Linux moments. Instant laugh for the right audience.

---

## 4. 30-Year Uptime Hover
- **Trigger:** Hovering over (or clicking) the `uptime` stat in the htop Skills section
- **Output:** Tooltip: `up 262,800 hours, 1 user, load average: legacy_code, coffee, curiosity`
- **Why:** Frames Daniele's career as a system uptime stat — concise, clever, and specific.

---

## 5. `curl` API Endpoint (Source Code Secret)
- **Trigger:** Viewing page source reveals an HTML comment: `<!-- Pro tip: curl https://[domain]/api/me -->`
- **Output:** The `/api/me` endpoint returns `Content-Type: text/plain` with fake HTTP headers and a terse bio:
  ```
  HTTP/1.1 200 OK
  X-Experience: 30yrs
  X-Coffee-Consumed: unknown
  X-Availability: open

  Daniele Testa — programmer since 1994.
  Full-stack. All stacks. No compromises.
  ```
- **Why:** Developers who view source are self-selecting as the right audience. A functional API rewards them.

---

## 6. 404 Page — Kernel Panic
- **Trigger:** Any 404 / not-found URL
- **Output:** Full-screen kernel panic simulation — white text on black, fake register dump, fake stack trace:
  ```
  [  0.000000] Kernel panic — not syncing: Page not found
  [  0.000001] find_good_developer+0x1e/0x30
  [  0.000002] allocate_patience() failed
  [  0.000003] You should probably hire someone who knows what they're doing.
  ```
  A `[REBOOT →]` button returns to home.
- **Why:** 404 pages are a standard personality canvas. A kernel panic is authentic to the aesthetic.

---

## 7. Idle Terminal Possession
- **Trigger:** Hero terminal left idle and unfocused for 45+ seconds
- **Output:** The cursor starts typing on its own — a fake auto-session begins. Simulates:
  ```
  daniele@testa:~$ ssh prod-server-03
  Connected to prod-server-03.
  daniele@prod:~$ git log --oneline
  a3f1c2d fix: remove 10,000 lines of someone else's spaghetti
  b2e9f4a feat: coffee-driven refactor
  c1d8e3f chore: explain to junior why this works
  d0c7b2e fix: it was DNS. it's always DNS.
  e9b6a1d init: started coding (1994)
  ```
  Stops immediately when the user interacts again.
- **Why:** Shows Daniele's sense of humor about a 30-year career without a word of self-promotion. The git log says everything.

---

## Implementation Notes
- Easter eggs 1, 3, 4, 6, 7 require no backend
- Easter egg 5 requires a minimal serverless endpoint (static text response)
- Easter eggs should never interfere with primary navigation or accessibility
- Document easter eggs nowhere public — let visitors discover them
