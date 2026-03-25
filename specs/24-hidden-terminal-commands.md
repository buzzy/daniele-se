# Hidden Terminal Commands

20 undocumented commands that work in the hero terminal but are **never listed in `help` output**. Discoverable only by curious visitors who type things and try. All follow the standard output contract: 80–200ms simulated delay, typewriter animation capped at 1.5s, error color for failures.

---

## 1. `uptime`

```
 22:14:07 up 262,800 hours,  1 user,  load average: legacy_code, coffee, curiosity
```

`262,800` = 30 years × 365 × 24 — mathematically exact. Surfaces the htop tooltip as a real command.

---

## 2. `uname -a`

```
DanieleOS 30.4.0-lts #1 SMP PREEMPT_DYNAMIC 1994-present x86_64 GNU/Linux
Kernel: curiosity-driven | Scheduler: deadline | Arch: polyglot
```

Version `30.4.0-lts` echoes the man page version. `LTS` signals stability.

---

## 3. `ping daniele`

```
PING daniele (contact@[domain]): 56 data bytes
64 bytes from daniele: icmp_seq=0 ttl=64 time=24ms
64 bytes from daniele: icmp_seq=1 ttl=64 time=21ms
64 bytes from daniele: icmp_seq=2 ttl=64 time=19ms
^C
--- daniele ping statistics ---
3 packets transmitted, 3 received, 0% packet loss
Round-trip: he actually responds. Try: cat contact.txt
```

0% packet loss is a subtle availability promise. The last line routes directly to the contact section.

---

## 4. `git log`

```
commit e9b6a1d (HEAD -> main, origin/main)
Author: Daniele Testa <daniele@[domain]>
Date:   1994

    init: started coding. had no idea what the next 30 years would look like.

commit a3f1c2d
Date:   2004

    fix: remove 10,000 lines of someone else's spaghetti

commit b2e9f4a
Date:   2008

    feat: coffee-driven refactor (results: excellent)

commit c1d8e3f
Date:   2015

    chore: explain to junior developer why this cursed thing actually works

commit d0c7b2e
Date:   2019

    fix: it was DNS. it's always DNS.

commit f1a9b8c
Date:   2024

    refactor: this website. it was worth it.
```

Career autobiography as commit messages. The final commit is self-referential.

---

## 5. `git blame`

```
^e9b6a1d (Daniele Testa 1994) Line 1: First program. It printed "Hello World".
^a3f1c2d (Daniele Testa 1997) Line 2: First C program that actually ran on bare metal.
^b2e9f4a (Daniele Testa 2001) Line 3: First production system. Served real users.
^c1d8e3f (Daniele Testa 2008) Line 4: First time I fixed a bug at 2am in prod.
^d0c7b2e (Daniele Testa 2014) Line 5: First time I said "it's DNS" and was right.
^f1a9b8c (Daniele Testa 2024) Line 6: This line. Right now. You're reading it.

git blame: all lines authored by the same person.
No one else to blame.
```

---

## 6. `ps aux`

```
USER       PID  %CPU  %MEM  COMMAND
daniele      1   99.9  88.4  /usr/bin/thinking
daniele      2   85.2  42.1  /usr/bin/coding --language=whatever-is-needed
daniele      3   61.0  30.5  /usr/bin/debugging --strategy=rubber-duck
daniele      4   44.3  18.2  /usr/bin/coffee --shots=2 --loop
daniele      5   38.7  12.6  /usr/sbin/mentoring --patience=high
daniele      6    9.1   4.3  /usr/bin/sleep (rarely invoked)
daniele      7    0.2   0.1  /usr/lib/systemd/legacy-code-auditor [zombie]
```

`sleep (rarely invoked)` and the zombie process callback to the htop skills section.

---

## 7. `df -h`

```
Filesystem              Size    Used   Avail  Use%  Mounted on
/dev/experience          30Y    30Y      0     100%  /career
/dev/knowledge          ∞       64%     36%    64%   /brain
/dev/legacy-projects    800G   800G      0     100%  /war-stories
/dev/side-projects       50G    47G      3G     94%  /nights-and-weekends
tmpfs                   8.0G    6.2G    1.8G   78%   /current-sprint
/dev/patience            12G    11.9G   0.1G   99%   /code-reviews
```

`/dev/knowledge` at 64% of infinite size is a graceful humble brag. `/dev/patience` at 99% is darkly self-aware about code reviews.

---

## 8. `top`

```
top - 22:14:07 up 262800h,  1 user
Tasks: 80 total,  18 running,  44 sleeping,  11 zombie,  7 stopped
%Cpu(s): 99.9 us,  0.0 sy,  0.0 ni,  0.0 id
MiB Mem : 262144.0 total,  8192.0 free

Hint: the Skills section has the full process list.
Type 'ls skills' to open it.
```

The task counts (80/18/44/11/7) exactly match the htop skills status bar — connecting the terminal to the skills section as a breadcrumb.

---

## 9. `ssh prod` / `ssh daniele@[domain]`

```
Connecting to [domain]:22...
OpenSSH_9.0 negotiating...
Warning: Permanently added '[domain]' to the list of known hosts.
daniele@[domain]'s password:
[any input]
Permission denied (publickey).
Hint: You didn't think I'd leave password auth on, did you?
```

The publickey-only refusal is exactly what a hardened server returns. Also handles: `ssh prod`, `ssh prod-server-03`, `ssh root@prod`.

---

## 10. `vim` / `vi` / `vim .` / `vim README.md`

A Vim interface opens inside the terminal widget containing Daniele's bio as a commit-message-formatted text file.

**States:**
- `i` → `-- INSERT --` in status bar
- `:q` → exits silently
- `:wq` → exits with: `"daniele.txt" written. Nice try — you can't change the facts.`
- `:q!` → shows: `E37: No write since last change (use ! to override)` and does nothing
- `:q!!` → exits with: `Fine. Quitting without saving. Story of every rewrite project.`
- Any other `:command` → `E492: Not an editor command: <command>`

After exit: `Daily driver since 1998. Config is 900 lines. No, I will not switch to VS Code.`

---

## 11. `curl /api/me` / `curl https://[domain]/api/me`

```
HTTP/1.1 200 OK
Content-Type: text/plain
X-Experience: 30yrs
X-Languages: too-many-to-list
X-Coffee-Consumed: unknown
X-Availability: open

Daniele Testa — programmer since 1994.
Full-stack. All stacks. No compromises.

This endpoint also works for real. Check the page source for the hint.
```

Surfaces the secret `/api/me` endpoint as a runnable command. The last line points back at the HTML comment easter egg.

---

## 12. `cat /etc/passwd`

```
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
daniele:x:1000:1000:Daniele Testa,,,:/home/daniele:/bin/vim
postgres:x:103:107:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
redis:x:107:114::/var/run/redis:/usr/sbin/nologin
nginx:x:33:33:www-data:/var/www:/usr/sbin/nologin
docker:x:999:999:Docker Engine:/var/lib/docker:/bin/bash
coffee:x:666:666:Critical dependency:/dev/caffeine:/bin/bash
```

Daniele's shell is `/bin/vim`. `coffee` is UID 666 — the dependency the system cannot function without.

---

## 13. `SELECT * FROM daniele;`

Case-insensitive. Also triggers on: `SELECT * FROM daniele`

```
psql (16.1) — connected to daniele_portfolio

SELECT * FROM daniele;

 id  | name            | experience | languages | databases | status
-----+-----------------+------------+-----------+-----------+--------
 001 | Daniele Testa   | 30 years   | 18+       | 14+       | HIRING
(1 row)

Query executed in 0.003ms.
Hint: try  DESCRIBE daniele;
```

Then if `DESCRIBE daniele;` is typed:
```
 Column       | Type         | Notes
--------------+--------------+-------------------------------------------
 id           | SERIAL       | auto-incremented since 1994
 name         | VARCHAR(255) | NOT NULL
 experience   | INTERVAL     | 30 years, still accumulating
 coffee_level | FLOAT        | CONSTRAINT: must be > 0.0
 patience     | INTEGER      | CHECK (patience > 0) — just barely passing
 availability | BOOLEAN      | TRUE
(6 rows)
```

Two-step discovery. The schema constraints are personality.

---

## 14. `nmap localhost` / `nmap daniele` / `nmap 127.0.0.1`

```
Starting Nmap 7.93 ( https://nmap.org )
Nmap scan report for daniele (127.0.0.1)
Host is up (0.0003s latency).

PORT      STATE   SERVICE         VERSION
22/tcp    open    ssh             OpenSSH 9.0 (hire me)
80/tcp    open    http            nginx/1.25.3
443/tcp   open    https           nginx/1.25.3 (TLS 1.3)
5432/tcp  open    postgresql      PostgreSQL 16.1
6379/tcp  open    redis           Redis 7.2.3
8080/tcp  open    curiosity       always-on
9999/tcp  open    side-projects   perpetually-running

Nmap done: 1 IP address (1 host up) scanned in 0.30 seconds
```

Port 8080 `curiosity always-on` and 9999 `side-projects perpetually-running`. Port 22 has a hire-me hint in the service version field.

---

## 15. `history`

```
    1  gcc -O2 -o hello hello.c  # 1994
    2  perl -e 'print "it works\n"'  # 2004, immediately regretted
    3  mysql -u root -p  # 2006, regretted immediately
    4  vim /etc/nginx/nginx.conf  # still open in another tab
    5  kubectl get pods --all-namespaces  # 2am. don't ask.
    6  git push --force  # never again
    7  SELECT * FROM production WHERE id = 1; -- no WHERE clause. first draft.
    8  sudo rm -rf /  # hypothetically. obviously.
    9  ssh root@prod  # before I knew better
   10  curl https://[domain]/api/me  # try it. it actually works.
```

Each entry is a specific era or a shared developer war story. Entry 10 is a direct breadcrumb to the secret API.

---

## 16. Package Managers: `brew install daniele` / `apt install daniele` / `npm install daniele` / `pip install daniele` / `yum install daniele`

Each package manager gets its own authentic voice:

**brew:**
```
==> Downloading daniele-testa-30.4.0.tar.gz
==> Pouring daniele-testa-30.4.0.arm64_sonoma.bottle.tar.gz
🍺  /usr/local/Cellar/daniele/30.4.0: 80 files, 30 years of experience
```

**apt / apt-get:**
```
Reading package lists... Done
Building dependency tree... Done
The following NEW packages will be installed: daniele-testa
0 upgraded, 1 newly installed, 0 to remove.
Need to get 30yr/262800h of experience.
Unpacking daniele-testa (30.4.0) ...
Setting up daniele-testa (30.4.0) ...
update-alternatives: using /usr/bin/daniele as the default programmer.
```

**npm:**
```
added 1 package in 0.003s — no node_modules bloat, just experience.
```

**pip:**
```
Collecting daniele==30.4.0
  Downloading daniele-30.4.0.tar.gz (30 years)
Successfully installed daniele-30.4.0
Warning: This package has no external dependencies. It already has everything it needs.
```

**yum:**
```
Resolving Dependencies
--> Running transaction check
---> Package daniele-testa.x86_64 30.4.0-1 will be installed
--> Finished Dependency Resolution
Installed: daniele-testa.x86_64 30.4.0-1
Complete! (and then some)
```

---

## 17. `whoami --verbose`

```
daniele

uid=1000(daniele) gid=1000(daniele) groups=1000(daniele),
  4(adm), 24(cdrom), 27(sudo), 30(dip), 46(plugdev),
  122(lpadmin), 134(sambashare), 1001(backend), 1002(frontend),
  1003(databases), 1004(devops), 1005(linux-admin), 1006(systems-architect),
  1007(polyglot), 1008(production-battle-hardened), 1009(still-learning)

Last login: today.   First login: 1994.
```

`1009(still-learning)` at the end — after every impressive group, it closes on genuine intellectual humility.

---

## 18. `crontab -l`

```
# Daniele's crontab — last edited: always

# Daily
0  7  *  *  *  /usr/bin/coffee --shots=2 --before-keyboard
30 7  *  *  *  /usr/bin/read-release-notes --skim
0  9  *  *  *  /usr/bin/work --focus --no-meetings-before-10am

# Weekly
0  18 *  *  5  /usr/bin/deploy --env=prod  # Fridays. I know.

# Monthly
0  1  1  *  *  /usr/bin/update-vim-config --lines=+12

# Annually
0  0  1  1  *  /usr/bin/learn --something=new --drop=something=old

# Every 30 years
0  0  1  1  */30 /usr/bin/reflect --career --output=this-website

# @reboot
@reboot /usr/bin/curiosity --daemon --respawn-on-crash
```

`@reboot curiosity --daemon --respawn-on-crash` is the philosophical core of the site expressed as a system service.

---

## 19. `traceroute daniele` / `traceroute [domain]`

```
traceroute to daniele ([domain]), 30 hops max

 1  first-computer (1994)          0.001 ms — Olivetti M24. BASIC. No going back.
 2  first-real-job (1997)          8.300 ms — C on a 33MHz DSP. Sink or swim.
 3  internet-discovery (1999)     12.700 ms — This changes everything.
 4  first-linux-server (2001)      3.200 ms — Never went back to Windows.
 5  first-production-db (2003)    44.100 ms — PostgreSQL. Love at first query.
 6  first-team-lead (2007)        88.000 ms — Harder than I expected.
 7  microservices-era (2014)      22.500 ms — Solved some problems. Created others.
 8  cloud-native (2017)            6.800 ms — Kubernetes. It was DNS again.
 9  this-website (2024)            0.300 ms — Still shipping. Still curious.
10  you (now)                       * ms     — Thanks for tracing the route.

Destination reached in 30 hops.
```

Each hop is a career milestone. The final hop addressing the visitor directly is the most unexpected moment in the terminal.

---

## 20. `exit` (second invocation)

First `exit` (documented):
```
logout
There is no escape. This terminal has been running since 1994.
Use Ctrl+C if you must. It won't work either.
```

Second `exit` typed immediately after:
```
logout
...
...
Fine. But you'll be back.
daniele@portfolio:~$
```
Terminal clears itself and shows a fresh prompt — mimicking a session that exits and immediately respawns.

---

## Implementation Notes

- 19 of 20 commands require **zero backend** — all static strings keyed on input
- `curl /api/me` reuses the existing `/api/me` endpoint
- `vim` requires a two-state input handler: after rendering the vim screen, the input prompt changes to `:` (vim command mode) and only accepts valid vim quit commands before returning to `daniele@portfolio:~$`
- `SELECT * FROM daniele;` requires a two-step state machine — after the first output, the terminal watches for `DESCRIBE daniele;` as the follow-up command
- All commands are matched case-insensitively where appropriate (`select`, `SELECT`, `Select` all work)
- These commands do NOT appear in `tab completion` — they are discoverable only by typing
- Unknown flags on known commands also trigger hidden responses (e.g. `whoami --verbose` vs `whoami`)

## Command Quick Reference

| Command | Category |
|---|---|
| `uptime` | Unix classic |
| `uname -a` | Unix classic |
| `ping daniele` | Networking |
| `git log` | Git |
| `git blame` | Git |
| `ps aux` | Unix classic |
| `df -h` | Unix classic |
| `top` | Unix / skills bridge |
| `ssh prod` | Networking |
| `vim` | Tool reference |
| `curl /api/me` | API |
| `cat /etc/passwd` | Unix classic |
| `SELECT * FROM daniele;` | Database |
| `nmap localhost` | Networking |
| `history` | Unix classic |
| `brew/apt/npm/pip install daniele` | Package managers |
| `whoami --verbose` | Meta |
| `crontab -l` | Unix classic |
| `traceroute daniele` | Networking |
| `exit` (second time) | Meta |
