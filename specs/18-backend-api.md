# Backend API Specification

## Contact Form — `POST /api/contact`

### Request Schema (JSON)
```json
{
  "from": "string",
  "subject": "string",
  "message": "string",
  "timestamp": "string (ISO 8601, client-generated)",
  "website": ""
}
```
(`website` is a honeypot field — hidden in UI, never populated by real users)

### Validation Rules
| Field | Rules |
|---|---|
| `from` | Required, valid email (must contain `@` + dot in domain), max 254 chars |
| `subject` | Required, min 3 chars, max 120 chars, not blank/whitespace-only |
| `message` | Required, min 10 chars, max 4000 chars, not blank/whitespace-only |
| `timestamp` | Optional — logged only, ignored for logic |

- All fields: strip leading/trailing whitespace before validation
- Strip all HTML tags from all fields before storing/sending
- If `timestamp` is < 3 seconds before server receipt: reject (bot guard)

### Spam Protection
1. **Honeypot:** If `website` field is non-empty → silently return fake `200` success
2. **Duplicate guard:** SHA-256 hash of `from + subject + message`; stored in memory with 10-minute TTL — identical resubmission within window → silent fake success
3. **Keyword blocklist:** If message body matches patterns (`http://`, `https://`, `<a`, `viagra`, `casino`) → reject with 422
4. **Rate limiting:** 5 requests/minute per IP (enforced at Nginx level — see deployment spec)
5. **Time gate:** `timestamp` < 3 seconds before receipt → reject

### Email Delivered to Daniele
```
From: noreply@[domain]
Reply-To: <from field value>
To: daniele@[domain]
Subject: [daniele.se] <subject field value>
X-Mailer: daniele.se-contact-service/1.0
X-Originating-IP: <client IP>

--- NEW CONTACT MESSAGE ---
From:    <from>
Subject: <subject>
Date:    <server UTC timestamp: YYYY-MM-DD HH:MM:SS UTC>
IP:      <client IP>
---

<message body, verbatim>

--- END OF MESSAGE ---
```

### Response Schema

**`200` Success:**
```json
{ "status": "ok", "code": "SENT", "message": "Message delivered." }
```

**`422` Validation Error:**
```json
{ "status": "error", "code": "VALIDATION_FAILED", "field": "<field name>", "message": "<reason>" }
```

**`429` Rate Limited:**
```json
{ "status": "error", "code": "RATE_LIMITED", "message": "Too many requests. Try again later." }
```

**`500` Server Error:**
```json
{ "status": "error", "code": "INTERNAL_ERROR", "message": "Transmission failed. Try again later." }
```

**Honeypot / duplicate — silent fake `200`:**
```json
{ "status": "ok", "code": "SENT", "message": "Message delivered." }
```

---

### Terminal UI Messages (exact strings displayed to visitor)

**Submitting (shown immediately on click):**
```
> ESTABLISHING CONNECTION...
> ENCRYPTING PAYLOAD...
> TRANSMITTING...
```

**Success (`200`):**
```
> TRANSMISSION COMPLETE.
> MESSAGE DELIVERED TO TARGET.
> AWAITING RESPONSE...
[OK] UPLINK CLOSED.
```

**Validation error (`422`):**
```
> ERROR: VALIDATION FAILURE
> FIELD: <FIELD NAME IN UPPERCASE>
> <HUMAN-READABLE REASON IN UPPERCASE>
> TRANSMISSION ABORTED.
```

**Rate limited (`429`):**
```
> ERROR: UPLINK SATURATED.
> TOO MANY REQUESTS FROM THIS NODE.
> RETRY IN 60 SECONDS.
> TRANSMISSION ABORTED.
```

**Server error (`500`):**
```
> ERROR: UPLINK FAILURE.
> REMOTE HOST UNREACHABLE OR REJECTED PACKET.
> CODE: 500
> TRANSMISSION ABORTED.
```

**Honeypot/spam blocked** — shows identical to success (never reveal the block to sender).

---

## Easter Egg API — `GET /api/me`

### Response Headers
```
Content-Type: text/plain; charset=utf-8
X-Powered-By: 30-years-of-experience
X-Node: daniele-primary-1
X-Uptime: <server uptime in seconds — dynamic>
X-Coffee-Consumed: uncountable
X-Stack: linux,nginx,node,postgres,redis,docker,k8s,bash,and-more
Cache-Control: no-store
```

### Response Body (exact content)
```
================================================================================
 DANIELE TESTA // SYSTEM PROFILE
 daniele.se // v1.0.0
================================================================================

 IDENTITY
 --------
 Name     : Daniele Testa
 Role     : Software Engineer / Architect / Everything Else
 Location : Sweden
 Online   : https://[domain]

 EXPERIENCE
 ----------
 Active since  : ~1995
 Years online  : 30+
 Speciality    : Full-stack, infrastructure, "just make it work"

 LANGUAGES
 ---------
 C, C++, Java, Python, JavaScript, TypeScript, PHP, Ruby, Go,
 Bash, SQL, Perl, Assembly (yes, really), and whatever the job needs.

 DATABASES
 ---------
 PostgreSQL, MySQL, MariaDB, MSSQL, Oracle, SQLite,
 MongoDB, Redis, Elasticsearch, Cassandra, DynamoDB.

 INFRASTRUCTURE
 --------------
 Linux (Arch, Debian, Ubuntu, RHEL — pick one),
 Nginx, Apache, Docker, Kubernetes, Ansible, Terraform,
 CI/CD: GitHub Actions, GitLab CI, Jenkins, Drone.

 CLOUD
 -----
 AWS, GCP, Azure — provider-agnostic by principle.

 PHILOSOPHY
 ----------
 "If it runs on a computer, I can figure it out.
  If I haven't seen it before, give me an afternoon."

 CONTACT
 -------
 Email : daniele@[domain]
 Web   : https://[domain]
 Form  : https://[domain]/#contact

================================================================================
 [EOF] — You found the easter egg. Nice.
 Tip: try   curl -s https://[domain]/api/me   and pipe it somewhere fun.
================================================================================
```

### HTML Source Comment
Placed in `<head>` before `</head>`:
```html
<!--
  Hey, you read source code. Respect.
  Try this: curl -s https://[domain]/api/me
-->
```

---

## Service Implementation Notes

- Both services run as separate lightweight processes (Node.js preferred for consistency with the rest of the stack, or Python — Daniele's call)
- Managed by systemd with auto-restart on crash
- `/api/contact` listens on `127.0.0.1:3001` (internal only, never exposed directly)
- `/api/me` listens on `127.0.0.1:3002` (internal only)
- Nginx proxies `/api/contact` → `:3001` and `/api/me` → `:3002`
- SMTP: use system `sendmail`, or a configured SMTP relay (e.g. msmtp pointing at a real SMTP server)
- Logs: stdout → systemd journal (`journalctl -u daniele-contact -f`)
- Environment variables: `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`, `CONTACT_DESTINATION_EMAIL`, `KEYWORD_BLOCKLIST` (comma-separated)
