# Provider configuration guide

Per-provider setup for IMAP/SMTP credentials. `mcp-email` uses **plain password or app password** authentication — no OAuth. Most modern providers require an **app password** (a per-application credential generated alongside 2FA).

For each provider below:
1. Where to get the app password (or whether your normal password works).
2. The IMAP/SMTP host + port to put in `.env`.
3. Known gotchas.

> If your provider isn't listed, the [Generic IMAP/SMTP](#generic-imapsmtp) recipe applies.

---

## Gmail / Google Workspace

**Requires 2-Step Verification + App Password.** Regular passwords no longer authenticate against IMAP/SMTP.

1. Turn on 2-Step Verification: <https://myaccount.google.com/security>
2. Generate an app password: <https://myaccount.google.com/apppasswords>
3. Choose "Mail" → device "Other" → name it `mcp-email`. Copy the 16-character password.

`.env`:
```env
ACCOUNT_gmail_EMAIL=you@gmail.com
ACCOUNT_gmail_PASSWORD=abcdabcdabcdabcd   # 16-char app password, no spaces
ACCOUNT_gmail_IMAP_HOST=imap.gmail.com
ACCOUNT_gmail_IMAP_PORT=993
ACCOUNT_gmail_SMTP_HOST=smtp.gmail.com
ACCOUNT_gmail_SMTP_PORT=465
```

**Gotchas:**
- Workspace admins can disable IMAP for the org. Check Admin Console → Apps → Google Workspace → Gmail → User settings.
- The "Sent" folder is `[Gmail]/Sent Mail`. Sent-folder auto-append may not land in the right place; see the README troubleshooting (shipped in the paid bundle).

---

## Hostinger

**Regular mailbox password works.** No app password needed.

`.env`:
```env
ACCOUNT_main_EMAIL=hello@yourdomain.com
ACCOUNT_main_PASSWORD=your_mailbox_password
ACCOUNT_main_IMAP_HOST=imap.hostinger.com
ACCOUNT_main_IMAP_PORT=993
ACCOUNT_main_SMTP_HOST=smtp.hostinger.com
ACCOUNT_main_SMTP_PORT=465
```

**Gotchas:** none in practice — this is the provider the server was originally tested against.

---

## Fastmail

**Requires an App Password.**

1. Log in → Settings → Privacy & Security → Integrations → App Passwords.
2. Create a new password with scope "Mail (IMAP/POP/SMTP)".

`.env`:
```env
ACCOUNT_fastmail_EMAIL=you@fastmail.com
ACCOUNT_fastmail_PASSWORD=xxxxxxxxxxxxxxxx
ACCOUNT_fastmail_IMAP_HOST=imap.fastmail.com
ACCOUNT_fastmail_IMAP_PORT=993
ACCOUNT_fastmail_SMTP_HOST=smtp.fastmail.com
ACCOUNT_fastmail_SMTP_PORT=465
```

---

## iCloud Mail

**Requires Apple 2FA + App-Specific Password.**

1. Sign in: <https://account.apple.com>
2. Sign-In and Security → App-Specific Passwords → Generate.
3. Use your `@icloud.com` (or aliased) address as the username.

`.env`:
```env
ACCOUNT_icloud_EMAIL=you@icloud.com
ACCOUNT_icloud_PASSWORD=xxxx-xxxx-xxxx-xxxx
ACCOUNT_icloud_IMAP_HOST=imap.mail.me.com
ACCOUNT_icloud_IMAP_PORT=993
ACCOUNT_icloud_SMTP_HOST=smtp.mail.me.com
ACCOUNT_icloud_SMTP_PORT=587
```

**Note:** SMTP runs on **587 (STARTTLS)**, not 465. The server flags this automatically (`smtpSecure: false` when port ≠ 465).

---

## Outlook 365 / Microsoft 365

**Requires an App Password (and the org must allow basic auth or app passwords for SMTP/IMAP).**

1. <https://account.microsoft.com/security> → Advanced security options → App passwords.
2. Create one labeled `mcp-email`.

`.env`:
```env
ACCOUNT_outlook_EMAIL=you@outlook.com
ACCOUNT_outlook_PASSWORD=xxxxxxxxxxxxxxxx
ACCOUNT_outlook_IMAP_HOST=outlook.office365.com
ACCOUNT_outlook_IMAP_PORT=993
ACCOUNT_outlook_SMTP_HOST=smtp.office365.com
ACCOUNT_outlook_SMTP_PORT=587
```

**Gotchas:**
- Some tenants have disabled SMTP AUTH and IMAP entirely. If so, the auth call will fail with `5.7.139` — ask your admin.
- Microsoft has been deprecating basic auth for shared/Exchange Online mailboxes. App passwords still work for **personal Microsoft accounts**; for org accounts, your mileage may vary.

---

## Proton Mail (via Proton Bridge)

Proton Mail does not expose IMAP/SMTP directly. You need **Proton Bridge** running on the same machine (or accessible network-locally).

1. Install Proton Bridge: <https://proton.me/mail/bridge>.
2. Run it, sign in, expand the account row to find the Bridge-generated IMAP/SMTP credentials and ports.

`.env`:
```env
ACCOUNT_proton_EMAIL=you@proton.me
ACCOUNT_proton_PASSWORD=BRIDGE_GENERATED_PASSWORD
ACCOUNT_proton_IMAP_HOST=127.0.0.1
ACCOUNT_proton_IMAP_PORT=1143
ACCOUNT_proton_SMTP_HOST=127.0.0.1
ACCOUNT_proton_SMTP_PORT=1025
```

**Gotchas:**
- Bridge ports differ from standard IMAP/SMTP ports.
- Bridge uses STARTTLS, not implicit TLS. The server treats ports ≠ 465 as STARTTLS automatically.
- If `mcp-email` runs in Docker, `127.0.0.1` refers to the **container**, not the host. Use `host.docker.internal` on Mac/Windows, or run with `network_mode: host` on Linux (edit `docker-compose.yml`).

---

## Yahoo Mail

**Requires App Password.**

1. <https://login.yahoo.com/account/security> → Generate app password.

`.env`:
```env
ACCOUNT_yahoo_EMAIL=you@yahoo.com
ACCOUNT_yahoo_PASSWORD=xxxxxxxxxxxxxxxx
ACCOUNT_yahoo_IMAP_HOST=imap.mail.yahoo.com
ACCOUNT_yahoo_IMAP_PORT=993
ACCOUNT_yahoo_SMTP_HOST=smtp.mail.yahoo.com
ACCOUNT_yahoo_SMTP_PORT=465
```

---

## Zoho Mail

**Requires an App Password if 2FA is on; otherwise the mailbox password works.**

1. <https://accounts.zoho.com/home#security/app_password>.

`.env` (EU region — adapt for `.com` US tenants):
```env
ACCOUNT_zoho_EMAIL=you@yourdomain.com
ACCOUNT_zoho_PASSWORD=xxxxxxxxxxxxxxxx
ACCOUNT_zoho_IMAP_HOST=imap.zoho.eu
ACCOUNT_zoho_IMAP_PORT=993
ACCOUNT_zoho_SMTP_HOST=smtp.zoho.eu
ACCOUNT_zoho_SMTP_PORT=465
```

US tenants: replace `.eu` with `.com`.

---

## Generic IMAP/SMTP

Any standards-compliant host works. Ask your provider (or check their docs) for:
- IMAP host + port + TLS mode (implicit TLS = port 993; STARTTLS = port 143 — **STARTTLS on 143 is not currently supported**, use 993).
- SMTP host + port + TLS mode (implicit TLS = port 465; STARTTLS = port 587 — both supported).

```env
ACCOUNT_custom_EMAIL=you@example.com
ACCOUNT_custom_PASSWORD=...
ACCOUNT_custom_IMAP_HOST=imap.example.com
ACCOUNT_custom_IMAP_PORT=993
ACCOUNT_custom_SMTP_HOST=smtp.example.com
ACCOUNT_custom_SMTP_PORT=465        # or 587 for STARTTLS
```

The server picks TLS mode automatically:
- **IMAP**: always implicit TLS (port 993 assumed).
- **SMTP**: implicit TLS when `SMTP_PORT=465`, STARTTLS otherwise (587, 25, etc.).

---

## Testing an account quickly

Once configured (see the README shipped in the paid bundle for the full install steps):

```bash
curl http://localhost:3000/health
# → accounts listed?
```

Then call a tool via Claude Code, or directly via `curl`:

```bash
curl -X POST http://localhost:3000/mcp \
  -H "Authorization: Bearer $MCP_AUTH_TOKEN" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json, text/event-stream" \
  -d '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"tools/call",
    "params":{
      "name":"email_list_folders",
      "arguments":{"account":"main"}
    }
  }'
```

If you get a JSON array of folders back, you're done. If the call hangs or returns an auth error, double-check the credentials and host/port for that account — most setup pain comes from app passwords not being generated correctly, or the wrong SMTP port (465 vs 587).
