# User Journeys & Conversion Goals

## Three Visitor Personas

---

## Persona 1: Potential Employer / Recruiter

### Profile
Arrives via LinkedIn, job boards, or a referral. Has a role to fill. Scanning for experience breadth and credibility. Time-sensitive.

### Ideal Path
1. **BIOS preloader** — Signals immediately this is not a template. Boot sequence flashes recognizable tech names (kernel versions, service names, mount points) to prime them.
2. **Terminal hero** — They watch or skip the typewriter sequence. The tagline must include "available for hire" or "open to opportunities."
3. **About (man page)** — Reads like a CV summary. SYNOPSIS must land the pitch in one line. DESCRIPTION covers breadth claim + years of experience.
4. **Skills (htop)** — Bar widths and category groupings confirm the "name any technology" claim without reading. 30+ technologies across 5–6 categories, all with high CPU%.
5. **Projects (dossier cards)** — Scans for company-recognizable outcomes: scale, impact, tech stack. Opens one modal to verify claims.
6. **Contact form** — Submits.

### Key Impression Moments
- htop: genuine breadth of technologies with high proficiency bars
- man page: punchy SYNOPSIS + concrete EXAMPLES section with measurable outcomes
- Project modal: outcomes language, not just description

### Call to Action
- Contact form submission
- Subject pre-populated with "Opportunity" when navigating from About or Skills sections
- Fallback: visible email link in contact block

---

## Persona 2: Fellow Developer (social link referral)

### Profile
Arrives via Twitter/X, Hacker News, Reddit, or a friend's share. Skeptical, playful, technically literate. Will poke at everything.

### Ideal Path
1. **BIOS preloader** — Earns a grin. They immediately understand the reference.
2. **Terminal hero** — They WILL type commands. They will try `sudo`, unknown commands, `exit`. They discover at least one easter egg within 60 seconds, which hooks them.
3. **Easter eggs** — The idle terminal possession, Konami code, or `sudo` response extends their visit and triggers a share impulse.
4. **Projects (modal detail)** — They're interested in technical decisions, not outcomes. The modal must show the tech stack, architecture notes, and a GitHub link.
5. **Skills (htop)** — Curiosity about what Daniele considers their strongest skills. Fun facts in tooltips are what matter here.
6. **Social links** — They follow on GitHub or connect on LinkedIn.

### Key Impression Moments
- The easter eggs — the site must reward curiosity
- Terminal commands that feel real and have character
- Project modal: technical depth, GitHub link, honest MISSION LOG

### Call to Action
- GitHub follow or LinkedIn connection (primary)
- Contact form (secondary)
- GitHub handle must be visible in the tmux status bar at all times

---

## Persona 3: Potential Client / Contractor

### Profile
Has a specific problem or project. Arrives via search, referral, or portfolio. Evaluating whether Daniele can solve their problem. Results-focused, may be non-technical.

### Ideal Path
1. **BIOS preloader** — Must be skippable immediately and obviously. Skip is shown after 1 second.
2. **Terminal hero** — Tagline must include "available for consulting" / "open to contracts." Hero is quickly bypassed via the tmux nav bar.
3. **Projects (cards)** — They navigate directly here. Domain tags (FINTECH, DEVOPS-TOOLING, E-COMMERCE, etc.) allow self-filtering. They scan for "has Daniele built something like what I need?"
4. **Project modal** — Opens 1–2 modals. They want: what was the problem, what did Daniele build, what was the result. Language must be outcome-first, not tech-first.
5. **Contact form** — Submits with a project description.

### Key Impression Moments
- Domain tags on project cards — instant relevance filter
- Project modal outcome language — answers "can you build what I need?"
- Contact form response time: "Typically responds within 24 hours" shown below the form

### Call to Action
- Contact form submission (sole conversion for this persona)
- CTA button label: "Start a conversation" or "Get in touch about your project" (not terminal-themed — clarity wins here)
- The sendmail aesthetic is maintained but the button copy is unambiguous

---

## Cross-Cutting UX Rules Derived from Personas

| Rule | Rationale |
|---|---|
| Availability status always visible (tmux bar + contact block) | All 3 personas need to see if Daniele is open before investing time |
| Skip preloader after 1s (not 1.5s) | Persona 3 is most impatient; persona 2 will watch it anyway |
| GitHub handle in tmux bar | Persona 2 converts via GitHub; it must always be findable |
| Project domain tags required | Persona 3 self-filters; without tags they bounce |
| Contact subject pre-fill from referrer section | Persona 1 context arrives pre-seeded, reducing friction |
| Response time note on contact form | Persona 3 needs trust signal before submitting |
| Project modal: outcome-first language | Persona 1 and 3 care about results; persona 2 will dig deeper regardless |
