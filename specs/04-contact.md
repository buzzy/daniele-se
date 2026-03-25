# Contact — `sendmail` Terminal Form

## Concept
The contact section is styled as composing a message via a Unix `mail`/`sendmail` command. The visitor fills in fields as if operating a terminal mail client.

## Entry
Section header renders as a shell prompt:
```
daniele@testa.dev:~$ mail
```

## Form Fields
Inputs appear sequentially, styled as terminal prompts:
```
To: daniele@testa.dev [auto-filled, read-only]
From: [visitor types their email]
Subject: [visitor types subject]
>
> [multi-line textarea — body of message]
> [Ctrl+D or click SEND to submit]
```

## Submit Interaction
- Submit button styled as: `[SEND MESSAGE ↵]` or `^D to send`
- On success: fake SMTP response displayed:
  ```
  250 OK: Message queued for delivery
  Message-ID: <a3f9c2d1@daniele.testa.dev>
  ```
- On error: red error output:
  ```
  550 Error: Message delivery failed — check your input and retry
  ```

## Direct Contact Details
Below the form — rendered as a `cat contact.txt` output block:
```
$ cat contact.txt

EMAIL     daniele@[domain]
GITHUB    github.com/[handle]
LINKEDIN  linkedin.com/in/[handle]
LOCATION  [City, Country]
STATUS    OPEN TO OPPORTUNITIES / NOT AVAILABLE (switchable)
```
Links are styled as terminal hyperlinks (underlined, terminal color).

## Availability Status
- A visible status indicator: `● AVAILABLE FOR HIRE` (green) or `● NOT CURRENTLY AVAILABLE` (amber/red)
- Shown in both the contact block and possibly in the tmux status bar

## Backend
- Form submission via a simple email relay (Resend, Formspree, or minimal serverless function)
- No captcha visible — use honeypot field or rate limiting instead (keeps the aesthetic clean)

## Social Links
Styled as a directory listing beneath the contact block:
```
drwxr-xr-x  github     github.com/[handle]
drwxr-xr-x  linkedin   linkedin.com/in/[handle]
-rw-r--r--  email      daniele@[domain]
```
