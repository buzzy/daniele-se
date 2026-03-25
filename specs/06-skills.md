# Skills — htop-Style Process Monitor

## Concept
A full-width terminal panel styled as a fake `top`/`htop` process monitor. Each technology Daniele knows is a "running process" with metadata that maps naturally to experience metrics.

## Header
```
daniele@testa:~$ top

Tasks: 80 total   |   uptime: 30 years   |   load average: 30.0 28.5 27.1
```

---

## Column Schema

| Column | Width | Description |
|---|---|---|
| PID | 5ch | Chronological ID — assigned in order of when the technology was first learned |
| NAME | 18ch | Technology name, monospace, left-aligned |
| CPU% | 6ch | Proficiency score (0.0–99.9) |
| MEM% | 6ch | Years of active use as % of 30-year career |
| UPTIME | 10ch | Rendered as `YYYYd HH:MM` (years since first use as days) |
| STATUS | 8ch | `running` / `sleeping` / `zombie` / `stopped` |
| CATEGORY | 10ch | Color-coded category label |

### STATUS Semantics
- `running` — actively using today (green `●`)
- `sleeping` — used recently but not daily (yellow `●`)
- `zombie` — deprecated/legacy, kept for nostalgia (dim `●`)
- `stopped` — deliberately retired, would not use again (gray `●`)

---

## Categories (Color-Coded)

| Category | Color | Count |
|---|---|---|
| Languages | green | 18 |
| Databases | cyan | 14 |
| DevOps/Infra | amber | 16 |
| Frontend | magenta | 10 |
| Backend | blue | 12 |
| Tools | white | 10 |
| **Total** | | **80** |

### Technology Roster

**Languages (green):** C, C++, Java, Python, JavaScript, TypeScript, PHP, Ruby, Go, Rust, Bash, SQL, Perl, C#, Scala, Kotlin, Lua, Assembly (x86)

**Databases (cyan):** PostgreSQL, MySQL, SQLite, Oracle DB, MS SQL Server, MongoDB, Redis, Cassandra, Elasticsearch, DynamoDB, CockroachDB, InfluxDB, Neo4j, MariaDB

**DevOps/Infra (amber):** Linux, Docker, Kubernetes, Terraform, Ansible, Jenkins, GitHub Actions, Nginx, HAProxy, Prometheus, Grafana, Vault, Consul, Packer, Vagrant, Helm

**Frontend (magenta):** HTML/CSS, React, Vue, Angular, Svelte, HTMX, Tailwind CSS, SASS/SCSS, WebAssembly, Three.js

**Backend (blue):** Node.js, Django, FastAPI, Spring Boot, Laravel, Rails, Express, Gin, ASP.NET Core, GraphQL, REST/OpenAPI, gRPC

**Tools (white):** Git, Vim, tmux, VS Code, Jira, Postman, Wireshark, Make, CMake, Valgrind

*(Final roster to be confirmed by Daniele)*

---

## Initial State & Sort Order

- On section entry: sorted by **CPU% descending** (most proficient first)
- All filter active (no filter applied)
- Rows stagger in with 30ms delay each — slide-in-from-left animation at ~12px offset
- Header row renders immediately before data rows

---

## Sorting

- Click column header: ascending → descending → reset to default (CPU% desc)
- Active sort column: underlined + `▲` / `▼` glyph
- User-triggered sort: rows cross-fade in ~150ms (no stagger)
- String columns: lexicographic; STATUS: `running > sleeping > stopped > zombie`; UPTIME: numeric by total days

---

## Category Filter Buttons

`[F1] ALL  [F2] LANG  [F3] DB  [F4] DEVOPS  [F5] FRONT  [F6] BACK  [F7] TOOLS`

- Pressing a filter key (keyboard or click) shows only matching rows
- Non-matching rows fade out
- Active filter: inverted background (category color bg, black text)
- Single filter active at a time
- PID column re-numbered contiguously in display (original PIDs in tooltips)
- Row count status bar updates: `Tasks: 14/80 shown`
- Sort order preserved when switching filters

---

## Tooltips

- **Delay:** 400ms after hover start (prevents jitter on scroll)
- **Position:** right of row if space allows, otherwise left
- **Style:** terminal popup box with single-line border
- **Dismiss:** immediately on mouse-leave

### Tooltip Content (4 lines)
1. Name + version range (e.g., `PostgreSQL — v7.4 to v16`)
2. First loaded year (e.g., `First loaded: 2003`)
3. Fun fact / war story — one sentence, first person, includes a concrete metric
4. STATUS reason — why it has its current status tag

### Sample Fun Facts
- **C** — `Wrote a real-time audio codec in C in 1997 that ran on a 33MHz DSP with 4KB of RAM.`
- **Perl** — `[zombie] Used Perl to scrape 2 million product pages overnight in 2004; still the fastest I ever moved data.`
- **PostgreSQL** — `Tuned a single PG instance to handle 180K writes/sec by rewriting the WAL flush strategy on bare metal.`
- **Redis** — `Replaced a 40-table MySQL schema with a Redis structure in one afternoon; query time dropped from 800ms to 0.3ms.`
- **Kubernetes** — `Debugged a cascading OOMKill loop across 200 pods at 2am by reading raw etcd keys directly.`
- **Assembly (x86)** — `[sleeping] Hand-optimized an inner loop in x86 ASM that made a physics simulation run 18x faster than the C compiler output.`
- **Nginx** — `Configured Nginx as a zero-downtime blue/green switch using Lua scripts before Kubernetes made it fashionable.`
- **Rust** — `Rewrote a Python service in Rust; memory dropped from 2.1GB to 38MB with identical throughput.`
- **Three.js** — `Built a real-time 3D network topology visualizer rendering 10,000 live nodes in-browser.`
- **Vim** — `[running] Daily driver since 1998. Config is 900 lines and counting.`
- **Cassandra** — `Managed a 48-node Cassandra ring doing 1.2M writes/minute for an IoT platform.`
- **WebAssembly** — `Compiled a legacy C++ image processing library to WASM to run inside a browser extension with no server round-trip.`

*(All fun facts to be reviewed / supplemented by Daniele)*

---

## Panel Overflow & Scroll

- Panel height: **60vh** fixed; header and filter buttons sticky
- Data rows scroll internally
- Scrollbar: thin 2px terminal-green line, no track (styled)
- Keyboard navigation when panel focused: ↑↓ = 1 row, PgUp/PgDn = 10 rows, Home/End = first/last
- When filter reduces list to ≤ 15 items: panel height shrinks to fit (min 200px, 200ms CSS transition)

---

## Row Count Status Bar (bottom of panel)

```
Tasks: 80, 18 running, 44 sleeping, 11 zombie, 7 stopped | Load average: 30.0 28.5 27.1
```

(Numbers are static flavour text)

---

## Uptime Easter Egg

Hovering the uptime stat in the panel header shows a tooltip:
```
up 262,800 hours, 1 user, load average: legacy_code, coffee, curiosity
```

---

## Accessibility

- Filter buttons: `aria-pressed` state
- Column headers: `role="columnheader"` + `aria-sort`
- Tooltip content available via `aria-describedby` on each row (points to visually hidden element)
- Keyboard users: Tab to panel → arrow keys navigate rows → Enter opens tooltip as modal overlay
- Touch: tooltip on tap, dismiss on second tap or tap-outside
