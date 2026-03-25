# About — `man daniele` Page

## Concept
The About section is styled as a Unix man page, triggered by navigating to it or typing `man daniele` in the hero terminal. It reads like an authoritative system manual entry — the most information-dense, universally recognized terminal artifact.

## Navigation
- Accessible via the tmux nav bar, `man daniele` in the hero terminal, or `whoami` command
- `man daniele` in the hero terminal: scrolls to this section with a pager animation; `(END)` shown at bottom; `q` returns to top
- Styled as a `less`/`more` pager: fixed-width monospace, scrollable, faint scanline

## Man Page Sections

### NAME
```
daniele-testa - Full-Stack Engineer, Systems Architect, Linux Veteran
```

### SYNOPSIS
```
daniele [--hire] [--collaborate] [--consult] [language] [stack] [problem]
```

### DESCRIPTION
Two to three paragraph bio covering:
- The arc from 1994 (or whenever Daniele started) to present
- Breadth of domains: languages, databases, Linux, deployment pipelines
- Philosophy: pragmatic, production-obsessed, polyglot by nature

### OPTIONS
Key traits formatted as CLI flags:
- `--pragmatic` — ships working software over perfect architecture
- `--polyglot` — language-agnostic; picks the right tool, not the familiar one
- `--production-obsessed` — has operated systems at scale; understands the difference between demo and deploy
- `--full-stack` — from bare-metal Linux to browser pixels
- `--systems-thinker` — sees the whole pipeline, not just the ticket

### FILES
Technologies listed as file paths grouped by category:
```
/usr/bin/python   /usr/bin/node   /usr/bin/go   /usr/bin/rust
/etc/postgresql   /etc/mysql      /var/lib/redis  /var/lib/mongo
/usr/sbin/nginx   /usr/bin/docker /usr/bin/kubectl
```

### EXAMPLES
3–5 career highlights formatted as command + output pairs:
```
$ daniele --fix legacy-monolith
> Refactored 200k LOC Java codebase to microservices. Zero downtime.

$ daniele --build real-time-system
> Delivered WebSocket-based platform serving 10k concurrent users.

$ daniele --setup infra from-scratch
> Provisioned and hardened multi-region Linux cluster with full CI/CD pipeline.
```
(Actual examples to be provided by Daniele)

### SEE ALSO
```
projects(1), contact(1), github(1), linkedin(1)
```

### BUGS
```
Occasionally over-engineers solutions. See also: this website.
Known issue: may spontaneously refactor your codebase if given access.
```

### VERSION
```
daniele 30.4 (stable)   Built: 1994   Last updated: 2026
```

## Visual Treatment
- Section headers: uppercase bold, left-aligned
- Body text: hanging indent, exactly as Unix man pages render
- Font: monospace (JetBrains Mono or IBM Plex Mono)
- Color: terminal green or amber on near-black
- Faint scanline overlay
- Blinking cursor at the end of the last line
