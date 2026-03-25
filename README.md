# daniele-se — Personal Website Specification

Personal website for **Daniele Testa**, a programmer with 30+ years of experience across languages, databases, Linux server administration, and deployment pipelines. The site is designed to reflect extreme knowledge breadth through a visually stunning, high-tech "movie OS" aesthetic — a terminal-based interactive experience that feels like operating a real system.

## Concept in One Sentence

A sci-fi movie-quality personal portfolio that IS a terminal: the visitor navigates it by typing commands, exploring skills as a fake `htop` process monitor, projects as classified dossier cards, and a bio written as a Unix man page.

## Spec Files

| File | Contents |
|---|---|
| [01-overview.md](specs/01-overview.md) | Project goals, target audience, core sections |
| [02-design.md](specs/02-design.md) | Aesthetic direction, color, typography, navigation overview |
| [03-projects.md](specs/03-projects.md) | Dossier cards, modal detail view, filtering |
| [04-contact.md](specs/04-contact.md) | Sendmail-style form, contact details, social links |
| [05-hero.md](specs/05-hero.md) | Interactive terminal prompt — all commands, UX edge cases, mobile |
| [06-skills.md](specs/06-skills.md) | htop process monitor — columns, 80-tech roster, tooltips, sorting |
| [07-about.md](specs/07-about.md) | Unix man page format — all sections, visual treatment |
| [08-easter-eggs.md](specs/08-easter-eggs.md) | All 7 easter eggs — triggers, outputs, implementation notes |
| [09-loading-performance.md](specs/09-loading-performance.md) | BIOS preloader, performance targets, asset strategy |
| [10-mobile-responsive.md](specs/10-mobile-responsive.md) | Mobile adaptations per section, breakpoints, touch targets |
| [11-seo-accessibility.md](specs/11-seo-accessibility.md) | HTML shell strategy, structured data, WCAG 2.1 AA requirements |
| [12-content-management.md](specs/12-content-management.md) | JSON data file schemas (projects, skills, config), update workflow |
| [13-deployment.md](specs/13-deployment.md) | Self-hosted Linux, Nginx config, git post-receive hook, security |
| [14-user-journeys.md](specs/14-user-journeys.md) | 3 visitor personas — ideal paths, conversion goals, UX rules |
| [15-visual-system.md](specs/15-visual-system.md) | Full design tokens: colors, typography, spacing, borders, glow, z-index |
| [16-nav-bar.md](specs/16-nav-bar.md) | Tmux status bar — exact layout, all elements, keyboard shortcuts, edge cases |
| [17-acceptance-criteria.md](specs/17-acceptance-criteria.md) | Complete launch checklist — 15 categories, 100+ criteria |
| [18-backend-api.md](specs/18-backend-api.md) | Contact form API contract, `/api/me` endpoint, spam protection |
| [19-tech-stack-and-structure.md](specs/19-tech-stack-and-structure.md) | Vanilla JS + Vite, directory layout, npm scripts, browser targets |
| [20-page-layout-and-scroll.md](specs/20-page-layout-and-scroll.md) | HTML structure, section ordering, scroll behavior, icons, 404 |
| [21-error-states-and-offline.md](specs/21-error-states-and-offline.md) | Failed fetches, broken images, JS-disabled, graceful degradation |
| [22-typewriter-primitive.md](specs/22-typewriter-primitive.md) | Reusable typewriter animation API, timing model, cancellation |
