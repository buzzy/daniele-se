# Content Management

## Strategy — JSON Data Files

All dynamic content lives in version-controlled JSON files. The JavaScript reads from these at runtime or they are inlined at build time. Adding a project or changing availability requires editing a JSON file, committing, and pushing — no rebuild of application logic.

---

## `/data/projects.json`

Array of project objects. Full schema (see also `specs/21-error-states-and-offline.md` for failure handling):

```json
[
  {
    "id": "project-slug",
    "referenceId": "PRJ-0042",
    "name": "Project Real Name",
    "codename": "OPERATION MIDNIGHT REFACTOR",
    "tagline": "One-sentence dramatic teaser for the card.",
    "description": "2–3 sentence plain-language summary displayed on the card.",
    "longDescription": [
      "First full paragraph — the problem or context.",
      "Second paragraph — what was built and the key decisions.",
      "Third paragraph — the outcome, scale, or measurable result."
    ],
    "tags": ["PostgreSQL", "Node.js", "Docker"],
    "domain": "FINTECH",
    "year": 2024,
    "url": "https://github.com/...",
    "demoUrl": "https://live-demo.example.com",
    "screenshotUrl": "/img/projects/project-slug.png",
    "featured": false,
    "status": "active",
    "classificationLevel": "PUBLIC",
    "missionLog": [
      { "date": "2023-11", "event": "Initial architecture deployed to production." },
      { "date": "2024-01", "event": "Scaled to 50K daily active users." }
    ]
  }
]
```

**Field definitions:**
| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | Yes | URL-safe slug |
| `referenceId` | string | Yes | Dossier ID e.g. `PRJ-0042` — padded to 4 digits |
| `name` | string | Yes | Real project name |
| `codename` | string | Yes | Dossier headline (stylized / dramatic) |
| `tagline` | string | No | One dramatic sentence shown on card; falls back to `description` |
| `description` | string | Yes | 2–3 sentences, plain language — shown on card |
| `longDescription` | string[] | Yes | Array of paragraphs shown in modal only (min 2) |
| `tags` | string[] | Yes | Tech stack — displayed as "SYSTEMS USED" |
| `domain` | string | Yes | Industry tag: `FINTECH`, `DEVOPS-TOOLING`, `E-COMMERCE`, `REAL-TIME`, `INFRA`, `DATA`, `SYSTEMS`, `OPEN-SOURCE`, `CONSULTING`, `INTERNAL` |
| `year` | number | Yes | Year initiated |
| `url` | string | Yes | Source or primary link |
| `demoUrl` | string | No | Live demo link |
| `screenshotUrl` | string | No | Path to screenshot image; terminal placeholder shown if missing |
| `featured` | boolean | Yes | If true, shown full-width above the grid |
| `status` | enum | Yes | `active` / `deployed` / `archived` / `stealth` |
| `classificationLevel` | enum | Yes | `PUBLIC` / `INTERNAL` / `RESTRICTED` |
| `missionLog` | object[] | No | Timeline entries `{date: "YYYY-MM", event: "string"}`; section hidden if absent |

**Status → badge mapping:**
| `status` | Badge | Color | Links |
|---|---|---|---|
| `active` | `ACTIVE` | cyan | Yes |
| `deployed` | `DEPLOYED` | green | Yes |
| `archived` | `ARCHIVED` | amber | Source only |
| `stealth` | `CLASSIFIED` | red | None |

**Minimum launch requirements:**
- At least 6 projects populated
- At least 1 with `featured: true`
- At least 1 with a complete `missionLog`
- At least 1 with `status: "stealth"` (provides a `CLASSIFIED` card)

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
