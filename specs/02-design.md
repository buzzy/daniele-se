# Design & Aesthetic

## Visual Direction
"High-tech server OS" — inspired by sci-fi movie interfaces, terminal UIs, and hacker aesthetics. The site should feel like an operating system or command-line environment come to life in the browser.

## Color Palette
- Background: near-black (e.g. `#0a0a0a` or `#0d1117`)
- Primary accent: terminal green (`#00ff41`) or cyan (`#00d4ff`)
- Secondary accent: amber (`#ffb000`) for warnings/badges
- Text: off-white or light gray for body; bright white for headings
- Cards/panels: slightly lighter dark (`#111827`) with glowing borders

## Typography
- Headings and UI chrome: monospace (e.g. JetBrains Mono, Fira Code, or IBM Plex Mono)
- Body text: monospace or a clean sans-serif for readability
- Terminal prompts: monospace with blinking cursor (`█`)

## Animations & Effects
- Typing/typewriter effects for key text
- Scanline overlay (subtle, CSS-based)
- Glitch flicker on hover states and transitions
- Boot/init sequence on first page load
- Blinking cursors on interactive elements
- Ambient animated background (subtle matrix rain, moving grid, or particle field)

## Navigation
**tmux-style fixed bottom status bar:**
- Always visible, styled as a tmux status line
- Left: session label `daniele@portfolio:~`
- Center: active section tabs (clickable), highlighted current section
- Right: live clock or uptime counter (e.g. `uptime: 30y 4m 12d`)
- Standard scroll also works to move between sections

## Responsiveness
- Desktop-first (primary experience)
- Mobile-friendly: navigation collapses gracefully, terminal interactions become tap-based
- Minimum breakpoint: 320px

## Tone
- Confident, technical, impressive
- No filler content — every element earns its place
- Interactive where possible — the user operates something, not just reads it
