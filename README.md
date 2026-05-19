# mcp-email

> **Self-hosted MCP server for IMAP and SMTP. Multi-account in one config. Built for Claude Code and any MCP-compliant client.**

[Buy on Gumroad — $49](https://shop.oneshotforge.com/l/mcp-email) · [Landing page](https://oneshotforge.com/mcp-email) · [Provider config docs](docs/CONFIG.md)

---

## What this repo is

This is the public face of `mcp-email`, a paid product from [OneShotForge](https://oneshotforge.com). The **source code lives in the paid bundle** sold on Gumroad. This repo contains:

- The README you're reading (features, scope, FAQ)
- The full per-provider config docs ([`docs/CONFIG.md`](docs/CONFIG.md))
- The public changelog ([`CHANGELOG.md`](CHANGELOG.md))
- The license terms ([`LICENSE.md`](LICENSE.md))

If you're shopping: this is enough to decide whether `mcp-email` fits your setup. The buy link is at the top.

If you've already purchased: the full TypeScript source, Docker setup, build configs, and complete installation guide are in the zip you received after checkout.

---

## Why this exists

Anthropic's MCP spec lets agents talk to your tools. Email is the most-asked tool. But the existing "MCP email" servers I tried either lock you to Gmail, demand an OAuth setup that takes an afternoon, or break the moment you point them at a second account.

`mcp-email` is the one I built for myself after that triage. I sell it because it now solves a problem cleanly enough that I'd rather get paid to maintain it than maintain it for free.

What's different:

- **Any IMAP/SMTP provider** — Gmail, Hostinger, Fastmail, iCloud, Outlook 365, Proton (via Bridge), Yahoo, Zoho, custom domains, anything that speaks the protocol.
- **Multi-account in one config** — declare N accounts via env vars, every tool call takes an `account` argument. One server instance speaks to all of them.
- **15 tools, prod-grade** — list, search, read, threads, attachments, send, draft, move, copy, delete, flag, folder CRUD. The boring operations are covered.
- **Self-hosted, credentials never leave your box** — runs locally via `npm` or in Docker. Bearer-token auth on the MCP endpoint. No telemetry, no SaaS proxy.
- **One `.env` file** — no OAuth dance, no service account JSON, no Google Cloud project. Generate app passwords on the provider side, drop them in, done.

---

## What you get when you buy

After checkout on [Gumroad](https://shop.oneshotforge.com/l/mcp-email), you download a single zip containing:

- Full **TypeScript source** for the server (Node 20+, `@modelcontextprotocol/sdk`, `imapflow`, `nodemailer`)
- **Dockerfile + docker-compose.yml** ready to run
- `.env.example` with the full template and inline comments
- The expanded README with install instructions, Claude Code wiring, production deployment via Caddy, and troubleshooting
- A **perpetual commercial license** (see [LICENSE.md](LICENSE.md) for the full terms — unlimited business use, no resale, refund within the first day if it doesn't fit)
- Free **patch and minor updates** within the 1.x major version

Approximate first-tool-call time after checkout: 5 minutes if your provider's app password is already generated.

---

## Provider matrix

I run on Hostinger and Gmail daily. Setup is documented and tested for the others.

| Provider | Authentication | IMAP host | SMTP host | Notes |
|---|---|---|---|---|
| **Hostinger** | Mailbox password | `imap.hostinger.com:993` | `smtp.hostinger.com:465` | What I use for `hello@oneshotforge.com`. 10/10 on mail-tester.com. |
| **Gmail / Workspace** | App password | `imap.gmail.com:993` | `smtp.gmail.com:465` | Enable 2FA first. Workspace admins can disable IMAP at the org level. |
| **Fastmail** | App password | `imap.fastmail.com:993` | `smtp.fastmail.com:465` | Clean implementation, no surprises. |
| **iCloud Mail** | App-specific password | `imap.mail.me.com:993` | `smtp.mail.me.com:587` | SMTP on 587/STARTTLS. |
| **Outlook 365** | App password | `outlook.office365.com:993` | `smtp.office365.com:587` | Some tenants disable SMTP AUTH. Personal accounts always work. |
| **Proton Mail** | Via Proton Bridge | `127.0.0.1:1143` | `127.0.0.1:1025` | Bridge must be running locally. |
| **Yahoo Mail** | App password | `imap.mail.yahoo.com:993` | `smtp.mail.yahoo.com:465` | App password under account security. |
| **Zoho Mail** | App password (or normal) | `imap.zoho.eu:993` / `.com` | `smtp.zoho.eu:465` / `.com` | EU vs US tenants use different hosts. |
| **Generic** | App password or normal | depends | depends | Anything RFC-compliant works. |

Full per-provider setup in [`docs/CONFIG.md`](docs/CONFIG.md).

---

## The 15 tools

All tools take an `account` parameter — the ID you declared in `.env`. Folders default to `INBOX` where applicable.

### Reading (6)

| Tool | Purpose |
|---|---|
| `email_list_folders` | List all IMAP folders with unread counts |
| `email_list_messages` | List messages in a folder with pagination and unseen filter |
| `email_search_messages` | Search by sender, subject, body, date, or has-attachment |
| `email_get_message` | Full message body (text + HTML + attachment metadata) by UID |
| `email_get_attachment` | Fetch an attachment's content (UTF-8 or base64) |
| `email_get_thread` | Reconstruct a conversation thread from Message-ID / In-Reply-To / References |

### Writing (2)

| Tool | Purpose |
|---|---|
| `email_send_message` | Send via SMTP, append a copy to Sent folder via IMAP |
| `email_create_draft` | Create a draft in the Drafts folder |

### Management (7)

| Tool | Purpose |
|---|---|
| `email_move_message` | Move a message to another folder |
| `email_copy_message` | Copy a message to another folder |
| `email_delete_message` | Move to Trash (default) or `\Deleted` + expunge |
| `email_mark_message` | Set or unset `\Seen` / `\Flagged` / `\Answered` flags |
| `email_create_folder` | Create an IMAP folder |
| `email_rename_folder` | Rename an IMAP folder |
| `email_delete_folder` | Delete an IMAP folder |

---

## How it connects to Claude Code

After install (covered in the zip), add this to your project `.mcp.json` or `~/.claude.json` global:

```json
{
  "mcpServers": {
    "email": {
      "type": "http",
      "url": "http://localhost:3000/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_MCP_AUTH_TOKEN"
      }
    }
  }
}
```

Restart Claude Code (`/mcp` to verify the server is `connected`), then try:

> *"List my last 5 unread emails in account `main`."*

Or, for advanced usage with multi-account:

> *"Search account `support` for unanswered messages from `customer@example.com` in the last 7 days, summarize them, and draft a reply in account `main`."*

---

## Pricing

**$49 one-time.** Perpetual commercial license, unlimited business use, no recurring fee.

Same-day refund if it doesn't fit your setup. Email `hello@oneshotforge.com` within 24 hours of buying and I'll process it (next morning if I'm asleep when you write).

[Buy on Gumroad](https://shop.oneshotforge.com/l/mcp-email)

---

## Limits and scope (intentional)

- **Authentication**: app password or plain password only. No OAuth 2.0. This keeps the server stateless and provider-agnostic.
- **Transport**: HTTP only (no stdio). MCP over HTTP is the only mode that supports remote agents and shared infrastructure.
- **Folder names**: defaults assume `INBOX`, `Sent`, `Drafts`, `Trash`. Servers with prefixes like `INBOX.Sent` are handled with a fallback. Custom names require small code changes.
- **Attachment download**: 1 MB cap for binary attachments (returned as base64). Textual attachments (text/*, JSON, XML, ICS) have no cap.
- **HTML body**: truncated past 25,000 characters in `email_get_message`. The text body is always sent in full.

---

## Roadmap (post-1.0, not committed)

- Optional `SENT_FOLDER` / `DRAFTS_FOLDER` / `TRASH_FOLDER` env overrides per account
- OAuth flow for Gmail and Microsoft (separate add-on, not bundled)
- Webhook on incoming mail (push-style notifications for agents)
- Calendar/ICS bridge (separate product, will integrate cleanly)
- AI auto-tagging via the Anthropic API

Vote with email — what do you want next? `hello@oneshotforge.com`.

---

## License

Commercial license — see [`LICENSE.md`](LICENSE.md).

TL;DR: unlimited personal and business use after purchase, no resale or redistribution of the source.

---

## Support

- Bugs and feature requests: email `hello@oneshotforge.com` with a clear repro.
- License questions, volume pricing: same address.
- Public changelog: [`CHANGELOG.md`](CHANGELOG.md).

Built by [OneShotForge](https://oneshotforge.com).
