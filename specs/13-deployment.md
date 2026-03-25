# Deployment & Hosting

## Strategy — Self-Hosted on Daniele's Linux Server

Hosting on Daniele's own infrastructure is both practical (no vendor dependency, full control) and narrative-consistent — the site is a demonstration of technical depth, and self-hosting is a live proof of that. Managed platforms are explicitly rejected in favor of owning the stack.

---

## Server Setup

### Web Server
- **Nginx** serving static files from web root
- HTTPS via **Let's Encrypt** (certbot with auto-renewal via systemd timer or cron)
- HTTP → HTTPS redirect configured at Nginx level
- Gzip compression enabled for HTML, CSS, JS, JSON, SVG
- Cache headers:
  - Static assets (fonts, images, CSS, JS with content hash): `Cache-Control: public, max-age=31536000, immutable`
  - `index.html` and JSON data files: `Cache-Control: no-cache` (must revalidate)

### Backend Services
Two lightweight services run behind Nginx reverse proxy, managed by **systemd**:

1. **`/api/me` service** — Minimal HTTP server (Node or Python) returning the plain-text curl easter egg response. Systemd unit ensures auto-restart on crash.
2. **Contact form relay** — Small service that receives POST requests from the contact form and forwards them as email via SMTP. Rate-limited at Nginx level (e.g., `limit_req_zone`). Returns JSON `{success: true}` or `{error: "..."}`.

---

## Deployment Pipeline

### Bare Git Repository + post-receive Hook

```bash
# On server: bare repo at /var/git/daniele-se.git
git init --bare /var/git/daniele-se.git

# post-receive hook:
#!/bin/bash
GIT_WORK_TREE=/var/www/daniele-se git checkout -f
nginx -s reload
```

### Developer Workflow
```bash
# On local machine: add server as remote
git remote add deploy user@server:/var/git/daniele-se.git

# Deploy
git push deploy master
```

Push triggers checkout to web root and Nginx reload. No external CI service required.

### Build Step (if applicable)
If the project uses a build step (e.g., Vite, esbuild), the hook runs the build:
```bash
#!/bin/bash
GIT_WORK_TREE=/tmp/daniele-se-build git checkout -f
cd /tmp/daniele-se-build && npm ci && npm run build
cp -r dist/* /var/www/daniele-se/
nginx -s reload
```

---

## Nginx Configuration (Sketch)

```nginx
server {
    listen 443 ssl http2;
    server_name [domain];

    root /var/www/daniele-se;
    index index.html;

    # Static assets — aggressive caching
    location ~* \.(js|css|woff2|png|svg|ico)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }

    # JSON data files — revalidate
    location /data/ {
        add_header Cache-Control "no-cache";
    }

    # API — proxy to backend service
    location /api/ {
        proxy_pass http://127.0.0.1:3001;
        limit_req zone=api burst=10 nodelay;
    }

    # Contact form
    location /send {
        proxy_pass http://127.0.0.1:3002;
        limit_req zone=contact burst=5 nodelay;
    }

    # SPA fallback
    location / {
        try_files $uri $uri/ /index.html;
    }

    ssl_certificate /etc/letsencrypt/live/[domain]/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/[domain]/privkey.pem;
}
```

---

## Security Hardening
- `X-Content-Type-Options: nosniff`
- `X-Frame-Options: DENY`
- `Content-Security-Policy` — restrict script sources, no inline eval
- `Referrer-Policy: no-referrer-when-downgrade`
- SSH access: key-based only, password auth disabled
- Firewall: only ports 22, 80, 443 open
- Contact form rate limiting: max 5 requests/minute per IP at Nginx

---

## Monitoring
- `systemd` watchdog for backend services
- Nginx access/error logs
- Let's Encrypt cert expiry monitoring (certbot auto-renew runs twice daily)
- Optional: lightweight uptime ping (e.g., UptimeRobot free tier)
