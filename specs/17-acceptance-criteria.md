# Acceptance Criteria — Launch Checklist

## 1. BIOS Preloader
- [ ] Displays on every cold page load for 1.5–4 seconds before transitioning
- [ ] Shows fake BIOS POST text: memory count, CPU detection, module loading
- [ ] Monospace font on black background with amber/green CRT color
- [ ] Skippable via Escape or `[SKIP]` button (visible after 1 second)
- [ ] Skipped entirely on repeat visits (sessionStorage flag)
- [ ] Does not replay on soft navigation / scroll
- [ ] Transition to hero is a deliberate scan-line wipe (not a fade)
- [ ] No layout shift or FOUC after preloader completes

## 2. Terminal Hero Section
- [ ] Full-viewport, convincing terminal window chrome (prompt, title)
- [ ] Welcome banner types out at full speed (≤ 1 second total)
- [ ] Cursor blinks after banner completes
- [ ] All documented commands are functional (help, whoami, cat about.txt, ls projects, ls skills, cat contact.txt, man daniele, clear, sudo, exit)
- [ ] Unknown commands return styled error with hint
- [ ] Tab completion works for known commands
- [ ] Command history (↑↓ keys) works across the session
- [ ] Ctrl+C clears input and prints `^C`
- [ ] Multi-line paste: only first line accepted
- [ ] Terminal max height 70vh, internal scroll
- [ ] Auto-scroll to bottom after each command (unless user scrolled up)
- [ ] 80–200ms simulated processing delay before output
- [ ] Skip button present and functional
- [ ] `prefers-reduced-motion`: typewriter animation disabled, content renders immediately

## 3. About — Man Page
- [ ] Styled unmistakably as a Unix man page
- [ ] Sections present: NAME, SYNOPSIS, DESCRIPTION, OPTIONS, FILES, EXAMPLES, SEE ALSO, BUGS, VERSION
- [ ] Content is accurate to Daniele's actual identity
- [ ] `man daniele` command in hero terminal navigates here
- [ ] SEE ALSO links to Projects and Contact sections
- [ ] Accessible at 200% zoom; text reflows at 320px

## 4. Skills — htop Monitor
- [ ] Panel header styled as htop (`top` command output)
- [ ] All 80 technologies present (or final confirmed roster)
- [ ] Columns: PID, NAME, CPU%, MEM%, UPTIME, STATUS, CATEGORY
- [ ] Color coding per category (green/cyan/amber/magenta/blue/white)
- [ ] STATUS semantics correct (running/sleeping/zombie/stopped)
- [ ] Sort by any column: click header for asc/desc/reset
- [ ] Category filter buttons functional (F1–F7)
- [ ] Active filter highlighted; row count shows `X/80`
- [ ] Tooltip appears after 400ms hover delay with 4-line content
- [ ] Animated row entry on section scroll-into-view (disabled with `prefers-reduced-motion`)
- [ ] Panel height 60vh; internal scroll; keyboard-navigable (↑↓, PgUp/PgDn, Home/End)
- [ ] Uptime hover easter egg functional
- [ ] Data loaded from `/data/skills.json`

## 5. Projects — Dossier Cards
- [ ] At least 6 project cards present at launch
- [ ] Each card: codename, reference ID, real name, status badge, domain tag, description, SYSTEMS USED, links, year
- [ ] Hover: scanline sweep + glitch border effect
- [ ] Click: modal opens with expanded detail (mission log, full description, links)
- [ ] Modal: backdrop click / Escape / CLOSE button all dismiss
- [ ] Modal: focus trapped inside; returns to card on close; body scroll locked
- [ ] Featured project shown full-width above grid
- [ ] Filter buttons functional (ALL / ACTIVE / DEPLOYED / ARCHIVED)
- [ ] 3-col → 2-col → 1-col grid responsive
- [ ] CLASSIFIED cards show description but hide links
- [ ] Empty state shown when filter has no results
- [ ] Data loaded from `/data/projects.json`
- [ ] Domain tags present on all cards

## 6. Contact — Sendmail Form
- [ ] Form styled as terminal `mail` command interface
- [ ] Fields: To (auto-filled read-only), From, Subject, Message — all required
- [ ] Client-side validation with accessible error messages
- [ ] Server-side validation and rate limiting in place
- [ ] Submission: "sending…" state, then SMTP-style success or error response
- [ ] Submissions delivered to Daniele's actual email (tested end-to-end)
- [ ] No plaintext email address in page source
- [ ] Availability status + response time note shown below form
- [ ] Social links (GitHub, LinkedIn at minimum) present in this section
- [ ] Subject pre-populated with "Opportunity" when navigating from About or Skills
- [ ] Form fully keyboard-operable

## 7. Tmux Navigation Bar
- [ ] Always visible at bottom of viewport, full width
- [ ] Three zones: session label | section tabs | uptime + availability + GitHub
- [ ] Active section tab highlighted (green bg, black text) while in view
- [ ] Click tab → smooth scroll to section
- [ ] Tab updates passively via IntersectionObserver on scroll
- [ ] Uptime counter updates every 60 seconds
- [ ] Availability badge reflects config value; dot pulses
- [ ] GitHub handle links to correct profile
- [ ] Keyboard shortcuts functional (Alt/Ctrl+1–4, Ctrl+Tab)
- [ ] Mobile: two-row layout, all critical info retained
- [ ] `prefers-reduced-motion`: blinking and pulsing disabled, scroll instant

## 8. Easter Eggs (all 7)
- [ ] Konami code → SYSTEM BREACH animation
- [ ] `man daniele` in terminal → pager scroll to About
- [ ] `sudo` → sudoers refusal response
- [ ] Uptime hover → "262,800 hours, load average: legacy_code, coffee, curiosity"
- [ ] Page source reveals curl tip; `/api/me` endpoint returns styled plain-text bio
- [ ] 404 page → kernel panic with stack trace
- [ ] Idle 45s → terminal auto-types fake git log
- [ ] No easter egg causes unhandled exceptions
- [ ] No easter egg breaks page state; all dismissible

## 9. Content Management (JSON)
- [ ] `/data/projects.json` schema validated and populated
- [ ] `/data/skills.json` schema validated and populated
- [ ] `/data/config.json` schema validated and populated (name, email, github, linkedin, availability, domain)
- [ ] Adding a project requires only a JSON edit — no code change
- [ ] JSON schema validation runs at build or deploy time

## 10. Performance
- [ ] Lighthouse Performance ≥ 90 desktop, ≥ 80 mobile
- [ ] FCP ≤ 1.5s on simulated 4G
- [ ] LCP ≤ 2.5s on desktop
- [ ] TBT ≤ 200ms
- [ ] CLS ≤ 0.1
- [ ] Total bundle ≤ 150KB gzipped
- [ ] All images: lazy-loaded, WebP/AVIF, descriptive `alt`
- [ ] Fonts: preloaded, `font-display: swap`, subset to used characters
- [ ] No render-blocking scripts without `defer`/`async`
- [ ] Canvas animation: < 4% CPU on mid-range laptop
- [ ] Canvas animation suspended when tab is hidden

## 11. Accessibility — WCAG 2.1 AA
- [ ] Lighthouse Accessibility ≥ 90
- [ ] All text: 4.5:1 contrast (3:1 for large text and UI components)
- [ ] All interactive elements keyboard-reachable and operable
- [ ] Focus indicators visible on all focusable elements
- [ ] All images/icons have `alt` or `aria-label`
- [ ] `prefers-reduced-motion` respected on all animations
- [ ] Single `<h1>`, logical heading hierarchy throughout
- [ ] All form inputs have associated `<label>`; errors programmatically associated
- [ ] Terminal output wrapped in `aria-live="polite"` region
- [ ] No color-only meaning anywhere
- [ ] Skip-to-content link present and functional (first focusable element)
- [ ] Tested with NVDA (Windows) or VoiceOver (macOS/iOS)
- [ ] Global "Pause animations" toggle present in nav bar

## 12. Cross-Browser Compatibility
- [ ] Chrome (latest stable): fully functional
- [ ] Firefox (latest stable): fully functional
- [ ] Safari (latest stable, macOS): fully functional
- [ ] Safari (iOS 15+): no critical layout breaks
- [ ] Edge (latest stable): fully functional
- [ ] Android Chrome (latest): no critical layout breaks
- [ ] Monospace font fallback chain renders correctly on font load failure
- [ ] All CSS features have fallbacks for non-supporting browsers

## 13. SEO & Discoverability
- [ ] `<title>` tag: `Daniele Testa — Full-Stack Developer`
- [ ] `<meta name="description">` present
- [ ] Open Graph tags complete (title, description, URL, image)
- [ ] Twitter Card meta tags present
- [ ] JSON-LD `Person` schema in `<head>`
- [ ] `robots.txt` allows crawl
- [ ] `sitemap.xml` present at `/sitemap.xml`
- [ ] Canonical URL set
- [ ] Static HTML shell contains all key content (name, bio, skills list, project list, contact links)
- [ ] Site loads correctly without JavaScript (HTML shell is readable)

## 14. Deployment & Security
- [ ] HTTPS with valid TLS certificate on correct domain
- [ ] HTTP → HTTPS redirect in place
- [ ] Security headers present: `X-Content-Type-Options`, `X-Frame-Options`, `Content-Security-Policy`, `Referrer-Policy`
- [ ] SSH: key-only auth, password auth disabled
- [ ] Firewall: only ports 22, 80, 443 open
- [ ] Contact form: Nginx rate limiting (max 5 req/min per IP)
- [ ] Backend services managed by systemd (auto-restart on crash)
- [ ] Let's Encrypt auto-renewal configured and tested
- [ ] Git post-receive deploy hook tested end-to-end

## 15. Wow Factor
- [ ] A person unfamiliar with the site, shown it cold, says "this looks like something from a movie" within 10 seconds — validated by at least 2 independent observers
- [ ] Every section has at least one interactive or animated element
- [ ] Visual design is internally consistent — no section looks like it was built separately
- [ ] No section feels "stock" or template-derived
- [ ] No unhandled JavaScript errors in browser console on any page load or interaction
- [ ] No placeholder text (`Lorem ipsum`, `TODO`, `FIXME`) anywhere in rendered output
- [ ] All external links open in new tab with `rel="noopener noreferrer"`
- [ ] Site personally reviewed and signed off by Daniele as accurately representing his identity and skill level
