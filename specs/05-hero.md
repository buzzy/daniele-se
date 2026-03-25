# Hero — Fullscreen Terminal Prompt

## Concept
The landing page IS a terminal. The entire first screen is a dark fullscreen terminal emulator. The visitor is dropped into an interactive shell and invited to explore.

## Entry Experience
- On load: a brief "boot sequence" plays (2–3 seconds of scrolling fake system init messages, e.g. `[OK] Mounting filesystem...`, `[OK] Loading daniele-testa kernel modules...`)
- Boot sequence resolves into a clean terminal prompt: `daniele@portfolio:~$`
- A blinking cursor awaits input
- Below the prompt: visible command chips / suggestions (clickable): `help` · `ls projects` · `cat about.txt` · `whoami` · `contact`
- A subtle line of text: `Type a command or click one above to begin.`

## Available Commands (Minimum)
| Command | Output |
|---|---|
| `help` | Lists all available commands with descriptions |
| `whoami` | Displays Daniele's name, title, years of experience |
| `cat about.txt` | Renders the About / Bio section |
| `ls projects` | Lists all projects (links to Project Showcase) |
| `ls skills` | Jumps to or renders the htop skills monitor |
| `cat contact.txt` | Displays contact details |
| `clear` | Clears the terminal output |
| `sudo ...` | Easter egg — humorous response |
| `exit` | Easter egg — refuses to exit, quips about the site |

## Skip Option
- A persistent `[SKIP →]` link in the top-right corner for visitors who don't want to interact
- Keyboard shortcut `Esc` or `Enter` (with empty input) can also skip to the full site scroll view

## Terminal Behavior
- Command history navigable with arrow keys (↑↓)
- Tab completion for known commands
- Unknown commands return a styled error: `bash: <cmd>: command not found — try 'help'`
- Output rendered with typewriter animation (fast, ~20ms per character)

## Transition to Full Site
- Typing `scroll` or using the tmux nav bar transitions to the scrollable single-page layout
- The hero terminal remains accessible by scrolling back to the top
