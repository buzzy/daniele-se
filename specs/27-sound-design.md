# Sound Design System

Complete Web Audio API sound architecture. All audio is opt-in — nothing plays until the user explicitly enables it via the `--sfx` flag in the tmux nav bar.

---

## Architecture

```
AudioContext
    └── MasterGainNode (0.0–1.0, default 0.7)
            ├── UIBus (GainNode, default 0.8)
            │       ├── keyclick channel
            │       ├── modal sounds
            │       └── nav/UI sounds
            ├── AmbientBus (GainNode, default 0.4)
            │       ├── CRT hum loop
            │       └── rain audio (future)
            └── MusicBus (GainNode, default 0.5)
                    └── hack mode ambient track
```

All buses are GainNodes routed to `MasterGainNode`. Individual sounds connect to a bus; users can adjust per-bus levels independently via the sub-controls panel.

**Voice Pool:** 16 simultaneous voices max. If all 16 are in use, the lowest-priority sound is cancelled. Priority tiers are defined per sound event (see table below).

---

## Toggle UI

The `--sfx` element in the tmux nav bar cycles through three states on click:

| State | Label | Icon |
|---|---|---|
| Off (default) | `--sfx=off` | `🔇` |
| UI only | `--sfx=ui` | `🔈` |
| UI + Ambient | `--sfx=full` | `🔊` |

Long-pressing `--sfx` (or clicking a `⚙` icon that appears on hover) opens a floating sub-controls panel anchored to the nav bar:

```
┌──────────────────────────────────────┐
│  Sound Controls                    × │
│                                      │
│  Master  ████████░░  80%             │
│  UI      ██████░░░░  60%             │
│  Ambient ████░░░░░░  40%             │
│  Music   █████░░░░░  50%             │
│                                      │
│  [Keyclick: Mechanical ▾]            │
│  [Test sound]                        │
└──────────────────────────────────────┘
```

All slider values stored in `localStorage` under `sfx_prefs`. Panel closes on Escape or click-outside.

---

## Sound Events

### 1. Keyclick — Standard

**Trigger:** Every character typed in any terminal input
**Bus:** UIBus
**Priority:** 3 (low — can be dropped if voice pool is full)
**Synthesis:**
- Square wave oscillator: 3400 Hz, 28ms duration
- Attack 2ms, decay 8ms, sustain 0, release 18ms
- Noise burst: white noise × 0.15 amplitude, 12ms
- Slight pitch randomization: ±200 Hz per keypress

**3 variants** (randomly selected per keypress to avoid repetition):
- Variant A: thicker click (square wave + sawtooth blend 60/40)
- Variant B: sharp click (square wave only, 3800 Hz)
- Variant C: softer click (triangle wave 2800 Hz, longer decay 20ms)

---

### 2. Keyclick — Backspace / Delete

**Trigger:** Backspace or Delete key
**Bus:** UIBus
**Priority:** 3
**Synthesis:** Same as keyclick but pitched down 600 Hz — gives a "removing" quality distinct from adding.

---

### 3. Keyclick — Enter / Command Execute

**Trigger:** Enter key submitting a terminal command
**Bus:** UIBus
**Priority:** 2
**Synthesis:**
- Two-tone: 440 Hz + 880 Hz, 60ms
- Hard attack (1ms), fast decay (40ms)
- Slight reverb tail (convolver, 80ms impulse)

---

### 4. Modal Open

**Trigger:** Project card modal opens
**Bus:** UIBus
**Priority:** 2
**Synthesis:** Three layers summed:
1. Noise burst: bandpass-filtered white noise (800–2400 Hz), 80ms
2. Sine sweep: 200 Hz → 600 Hz over 120ms
3. Transient click: square wave 2000 Hz, 15ms

Combined envelope: attack 5ms, decay 60ms, sustain 0.2, release 55ms

---

### 5. Modal Close

**Trigger:** Modal closes (×, Escape, backdrop click)
**Bus:** UIBus
**Priority:** 2
**Synthesis:** Reverse of modal open — sine sweep 600 Hz → 200 Hz, noise burst decaying, no transient. 90ms total.

---

### 6. Command Success (typewriter completion)

**Trigger:** Typewriter animation completes on a command output
**Bus:** UIBus
**Priority:** 3
**Synthesis:** Soft sine tone 880 Hz, 40ms, triangle envelope. Low amplitude (0.3).

---

### 7. Error / Permission Denied

**Trigger:** Terminal outputs an error line (permission denied, command not found, etc.)
**Bus:** UIBus
**Priority:** 2
**Synthesis:**
- Two-tone dissonant: 220 Hz + 233 Hz (minor second), 200ms
- Tremolo: 8 Hz AM modulation
- Slightly distorted via WaveShaperNode

---

### 8. CRT Hum Loop

**Trigger:** Enabled when sfx mode is `full` (UI + Ambient). Loops continuously.
**Bus:** AmbientBus
**Priority:** 1 (never dropped)
**Synthesis:**
- Base hum: sine wave 60 Hz (North American power line frequency), amplitude 0.08
- Harmonic: sine wave 120 Hz (second harmonic), amplitude 0.03
- High-frequency hiss: white noise × 0.02, highpass filter 8000 Hz
- Total perceived loudness: very quiet background texture only

**Intensity scaling:** During active terminal interaction (keypress within last 2s), amplitude increases by 20% with 400ms ease. Returns to base on inactivity.

---

### 9. Scan Line Sweep (Preloader)

**Trigger:** BIOS preloader scan-line wipe transition
**Bus:** UIBus
**Priority:** 2
**Synthesis:**
- White noise sweep: highpass filter sweeping 8000 Hz → 400 Hz over 800ms (synchronized to scan-line duration)
- Amplitude envelope: linear fade from 0.4 → 0

---

### 10. Section Scroll Transition

**Trigger:** Active section changes (IntersectionObserver fires)
**Bus:** UIBus
**Priority:** 3
**Synthesis:** Subtle: triangle wave 1200 Hz, 25ms, amplitude 0.15. Like a terminal screen tick.

---

### 11. Tab / Nav Click

**Trigger:** User clicks a tmux nav tab
**Bus:** UIBus
**Priority:** 3
**Synthesis:** Square wave 1600 Hz, 20ms, amplitude 0.2. Slightly different from keyclick to distinguish UI interaction from typing.

---

### 12. Copy Confirmation

**Trigger:** Clipboard copy success (email/social copy interaction)
**Bus:** UIBus
**Priority:** 2
**Synthesis:** Two-note ascending: 660 Hz then 880 Hz, 30ms each, 20ms gap. Positive confirmation tone.

---

### 13. Form Submission Success

**Trigger:** Contact form `250 OK: Message queued` line appears
**Bus:** UIBus
**Priority:** 1
**Synthesis:** Three-note ascending: 440 Hz, 554 Hz, 659 Hz (A-C#-E, major chord broken). 50ms per note, 30ms gaps. Celebratory but subtle.

---

### 14. Form Submission Error

**Trigger:** Contact form `550 5.1.1 User unknown` or network error
**Bus:** UIBus
**Priority:** 1
**Synthesis:** Descending two-note: 440 Hz → 311 Hz (A → Eb, tritone). 80ms each. Unmistakably error-toned without being alarming.

---

### 15. Konami Code — Dial-Up Modem Sequence

**Trigger:** Konami code entered
**Bus:** MusicBus
**Priority:** 1 (overrides everything)
**Duration:** ~8 seconds total

Full V.34 modem handshake synthesis sequence:

| Phase | Duration | Description |
|---|---|---|
| Dial tone | 800ms | 350 Hz + 440 Hz sine mix |
| Dialing | 1200ms | DTMF tones for a fake phone number (randomized) |
| Ring | 600ms | 480 Hz + 620 Hz, 2-cycle |
| Answer tone | 400ms | 2100 Hz sine |
| Handshake noise | 3000ms | AM-modulated white noise with chirps, sweeping filters 300–3400 Hz |
| Training sequence | 1200ms | Rapid alternating tones 1200 Hz / 2400 Hz |
| Connected | 800ms | Final tone 1200 Hz fading out |

After the modem sequence, `hack_ambient.js` begins playing on MusicBus if music is enabled:
- Looping ambient pad: slow LFO-modulated sine chords (C minor pentatonic)
- Subtle glitch artifacts every 8–15 seconds
- Fades out over 2s when Konami terminal is exited

---

## Priority Tier Reference

| Priority | Description | Behaviour when pool full |
|---|---|---|
| 1 | Critical (Konami, form result) | Never dropped; will cancel priority 3 voices |
| 2 | Standard UI feedback | Dropped only if 12+ voices active |
| 3 | Ambient/repetitive | First to be dropped |

---

## Layering / Clash Prevention

- **Keyclick deduplication:** If two keypresses arrive within 20ms (paste scenario), only one click fires.
- **Modal sounds:** If a modal opens while another modal close sound is playing, the close sound is immediately stopped (not faded) and the open sound starts.
- **CRT hum:** Always on its own dedicated audio node — never interrupted by UI sounds.
- **Konami sequence:** Temporarily fades UIBus to 0.2 and AmbientBus to 0.1 for the duration of the modem sequence, then restores.
- **Page visibility:** All buses fade to 0 over 200ms when `document.visibilityState === 'hidden'`. Restore to previous levels on focus return.

---

## Edge Cases

- **AudioContext autoplay policy:** All audio deferred until first user interaction (click/keypress). The sfx toggle itself counts as first interaction.
- **Safari compatibility:** `AudioContext` created as `new (window.AudioContext || window.webkitAudioContext)()`.
- **Mobile:** Sound is disabled by default and the enable option is still available, but a note in the sub-controls panel reads: `Note: audio on mobile requires tap to activate.`
- **prefers-reduced-motion:** Does not affect audio (motion preference ≠ audio preference). Audio follows its own `sfx_prefs` setting.
- **Fast navigation away:** `beforeunload` fades MasterGain to 0 over 100ms then calls `audioContext.suspend()`.
- **No SFX mode (off state):** `AudioContext` is never created — zero CPU overhead for users who never enable sound.
