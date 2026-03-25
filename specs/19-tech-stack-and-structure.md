# Technology Stack & Project Structure

## Stack Decision

Single-page application delivered as static files. Deliberately minimal — no heavy framework.

| Concern | Choice | Rationale |
|---|---|---|
| Runtime | Vanilla JavaScript (ES2022+) | No virtual DOM needed; interactivity is self-contained |
| Build tool | Vite (latest stable) | Fast HMR, native ES modules, content-hash filenames, minimal config |
| CSS | Plain CSS with custom properties | Design system is already token-based; preprocessor adds no value |
| Language | JavaScript (no TypeScript) | Small project, owner is sole developer; JSDoc used where helpful |
| Package manager | npm | Standard; no workspace complexity needed |

---

## Project Directory Layout

```
daniele-se/
├── index.html                  # Entry point — static HTML shell (SEO + no-JS fallback)
├── 404.html                    # Kernel panic page — standalone, no bundle dependency
├── vite.config.js
├── package.json
├── .nvmrc                      # Contains: 20
├── .browserslistrc
├── .gitignore
├── public/
│   ├── robots.txt
│   ├── sitemap.xml
│   ├── favicon.ico             # 32×32 + 16×16 multi-size ICO
│   ├── favicon.svg             # Modern browsers; dark-mode safe
│   ├── apple-touch-icon.png    # 180×180 for iOS bookmark
│   └── og-image.png            # 1200×630 OG/Twitter Card image
├── data/
│   ├── projects.json
│   ├── skills.json
│   └── config.json
├── src/
│   ├── main.js                 # Entry — initialises all modules after DOMContentLoaded
│   ├── preloader.js            # BIOS preloader (must be < 10KB standalone)
│   ├── terminal.js             # Hero terminal emulator
│   ├── skills.js               # htop skills panel
│   ├── projects.js             # Dossier card grid + modal
│   ├── contact.js              # Sendmail form + submission
│   ├── nav.js                  # Tmux status bar + IntersectionObserver
│   ├── background.js           # Canvas rain + grid underlay
│   ├── easter-eggs.js          # Konami code, idle possession, 404, etc.
│   ├── cursor.js               # Custom block cursor (desktop only)
│   └── utils/
│       ├── typewriter.js       # Reusable typewriter animation primitive
│       ├── scroll.js           # Smooth scroll helpers
│       └── data.js             # JSON fetch/cache utilities
├── css/
│   ├── tokens.css              # All CSS custom properties (design tokens)
│   ├── reset.css               # Minimal CSS reset
│   ├── global.css              # Body, scanline overlay, cursor, fonts
│   ├── preloader.css           # Preloader styles (inlined in <head>)
│   ├── terminal.css
│   ├── skills.css
│   ├── projects.css
│   ├── contact.css
│   ├── nav.css
│   ├── about.css
│   └── 404.css
├── api/
│   ├── contact/
│   │   ├── server.js           # POST /api/contact (Node.js, port 3001)
│   │   └── package.json
│   └── me/
│       ├── server.js           # GET /api/me (Node.js, port 3002)
│       └── package.json
└── scripts/
    └── validate-json.js        # Validates data/*.json at build time
```

---

## Vite Configuration

```js
import { defineConfig } from 'vite'

export default defineConfig({
  root: '.',
  publicDir: 'public',
  build: {
    outDir: 'dist',
    assetsDir: 'assets',
    rollupOptions: {
      input: {
        main: './index.html',
        '404': './404.html',
      },
    },
    cssCodeSplit: true,
    sourcemap: false,   // No sourcemaps in production
    minify: 'esbuild',
  },
  server: {
    port: 5173,
    proxy: {
      '/api': 'http://localhost:3001',
    },
  },
})
```

## npm Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview",
    "lint": "eslint src/ --ext .js",
    "validate-data": "node scripts/validate-json.js"
  }
}
```

## Requirements

- **Node.js:** 20 LTS minimum (`.nvmrc` file at project root contains `20`)
- **Browser targets** (`.browserslistrc`): last 2 versions of Chrome, Firefox, Safari, Edge — no IE 11
