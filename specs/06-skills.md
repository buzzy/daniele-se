# Skills — htop-Style Process Monitor

## Concept
A full-width terminal panel styled as a fake `top`/`htop` process monitor. Each technology Daniele knows is a "running process" with metadata that maps naturally to experience metrics.

## Header
```
daniele@testa:~$ top

Tasks: 87 total   |   uptime: 30 years, 4 months   |   load avg: HIGH
```

## Column Schema
| Column | Meaning |
|---|---|
| PID | Sequential fake ID |
| NAME | Technology name (e.g. `PostgreSQL`, `Rust`, `Linux`) |
| CPU% | Current relevance / active usage (0–99%) |
| MEM% | Depth of knowledge |
| UPTIME | Years of experience (e.g. `28y`, `6y`, `15y`) |
| STATUS | `RUNNING` / `IDLE` / `SLEEPING` |
| CATEGORY | Color-coded tag |

## Categories (Color-Coded)
- **Languages** — terminal green
- **Databases** — cyan
- **DevOps / Infrastructure** — amber
- **Frontend** — magenta/pink
- **Backend / Systems** — blue
- **Tools & Platforms** — gray/white

## Sample Technologies (to be populated by Daniele)
Languages: C, C++, Java, Python, JavaScript/TypeScript, PHP, Go, Rust, Bash, SQL, Perl, Ruby, Kotlin, Swift
Databases: PostgreSQL, MySQL, MariaDB, SQLite, MongoDB, Redis, Cassandra, Elasticsearch, DynamoDB
DevOps: Linux, Docker, Kubernetes, Terraform, Ansible, CI/CD, Nginx, AWS, GCP
Frontend: HTML/CSS, React, Vue, Svelte, WebSockets
Backend: Node.js, REST APIs, GraphQL, gRPC, microservices
Tools: Git, Vim, tmux, Make, Webpack, Vite

## Interactions
- Rows are sortable by column (click column header)
- Hovering a row highlights it and shows a tooltip with a brief description or fun fact
- Categories can be filtered via function-key-style buttons: `F1: ALL` `F2: LANG` `F3: DB` etc.
- The panel scrolls internally if the list exceeds screen height
- Numbers animate on section entry (CPU% and MEM% count up from 0)

## Visual Details
- Alternating row shading (very subtle)
- Category color applied to the NAME column text
- STATUS column uses colored indicators: green `●` for RUNNING, yellow `●` for IDLE, gray `●` for SLEEPING
- Panel header bar styled as an htop header with fake system stats
