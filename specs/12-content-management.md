# Content Management

## Strategy — JSON Data Files

All dynamic content lives in version-controlled JSON files. The JavaScript reads from these at runtime or they are inlined at build time. Adding a project or changing availability requires editing a JSON file, committing, and pushing — no rebuild of application logic.

---

## `/data/projects.json`

Array of project objects. Schema:

```json
[
  {
    "id": "project-slug",
    "name": "Project Name",
    "codename": "OPERATION CODENAME",
    "description": "1–2 sentence plain-language summary of what the project does.",
    "tags": ["PostgreSQL", "Node.js", "Docker"],
    "year": 2024,
    "url": "https://github.com/...",
    "demoUrl": "https://live-demo.example.com",
    "featured": true,
    "status": "active"
  }
]
```

**Field definitions:**
| Field | Type | Description |
|---|---|---|
| `id` | string | URL-safe slug |
| `name` | string | Real project name |
| `codename` | string | Dossier headline (stylized / dramatic) |
| `description` | string | 1–2 sentences, plain language |
| `tags` | string[] | Tech stack — displayed as "SYSTEMS USED" |
| `year` | number | Year initiated |
| `url` | string | Source or primary link |
| `demoUrl` | string? | Live demo link (optional) |
| `featured` | boolean | If true, shown full-width above the grid |
| `status` | enum | `active` / `archived` / `stealth` |

**Status display mapping:**
- `active` → `ACTIVE` badge (cyan)
- `archived` → `ARCHIVED` badge (amber)
- `stealth` → `CLASSIFIED` badge (red) — description shown but links hidden

---

## `/data/skills.json`

Grouped skill entries. Schema:

```json
[
  {
    "category": "Languages",
    "color": "green",
    "skills": [
      { "name": "Python", "cpu": 85, "mem": 90, "uptime": "15y", "status": "running" },
      { "name": "C", "cpu": 20, "mem": 95, "uptime": "28y", "status": "idle" }
    ]
  }
]
```

**Field definitions:**
| Field | Type | Description |
|---|---|---|
| `category` | string | Group label (Languages, Databases, DevOps, Frontend, Backend, Tools) |
| `color` | string | Category color key (`green`, `cyan`, `amber`, `magenta`, `blue`, `white`) |
| `name` | string | Technology name |
| `cpu` | number | Current active usage/relevance (0–99) |
| `mem` | number | Depth of knowledge (0–99) |
| `uptime` | string | Display string (e.g. `28y`, `6m`, `3y`) |
| `status` | enum | `running` / `idle` / `sleeping` |

---

## `/data/config.json`

Site-wide settings. Schema:

```json
{
  "availabilityStatus": "open",
  "availabilityNote": "Available for contracts from May 2026",
  "lastUpdated": "2026-03-25",
  "domain": "daniele.testa.dev",
  "email": "daniele@...",
  "github": "https://github.com/...",
  "linkedin": "https://linkedin.com/in/...",
  "location": "City, Country"
}
```

**`availabilityStatus` values:**
- `"open"` → `● OPEN TO WORK` (green) — shown in tmux bar and contact section
- `"limited"` → `● LIMITED AVAILABILITY` (amber)
- `"unavailable"` → `● NOT AVAILABLE` (red)

---

## Update Workflow

```bash
# Add a new project
vim data/projects.json
git add data/projects.json
git commit -m "feat: add project <name>"
git push

# Change availability
vim data/config.json
git add data/config.json
git commit -m "chore: update availability status"
git push
```

Deployment is triggered automatically by the push (CI/CD pipeline or static host webhook).
