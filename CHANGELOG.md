# Changelog

All notable changes to **mcp-email** are documented here.
Format loosely based on [Keep a Changelog](https://keepachangelog.com), versioning follows [SemVer](https://semver.org).

---

## [1.0.0] — 2026-05-13

First public release. Production-grade IMAP/SMTP MCP server, multi-account, provider-agnostic.

### Added
- **15 MCP tools** exposed over HTTP:
  - Reading: `email_list_folders`, `email_list_messages`, `email_search_messages`, `email_get_message`, `email_get_attachment`, `email_get_thread`.
  - Writing: `email_send_message` (with auto-append to Sent folder), `email_create_draft`.
  - Management: `email_move_message`, `email_copy_message`, `email_delete_message`, `email_mark_message`, `email_create_folder`, `email_rename_folder`, `email_delete_folder`.
- **Multi-account support** via `ACCOUNT_<id>_*` env pattern, with optional `DEFAULT_*` fallbacks for IMAP/SMTP host/port.
- **Provider docs** covering Gmail, Hostinger, Fastmail, iCloud, Outlook 365, Proton (via Bridge), Yahoo, Zoho, and generic IMAP/SMTP — see [`docs/CONFIG.md`](docs/CONFIG.md).
- **HTTP transport** via `@modelcontextprotocol/sdk` `StreamableHTTPServerTransport` in stateless JSON mode. Bearer-token auth on `POST /mcp`.
- **`/health` endpoint** for diagnostics — returns configured account IDs and surfaces config errors.
- **Docker support** via included `Dockerfile` + `docker-compose.yml`. Builds on `node:20-alpine`.
- **Attachment handling** — textual attachments (text/*, JSON, XML, ICS) returned as UTF-8; binary as base64 with a 1 MB cap.
- **Threading** — `email_get_thread` reconstructs conversations using `Message-ID` / `In-Reply-To` / `References`.
- **Sent / Drafts / Trash fallbacks** — code tries the standard folder name first, falls back to `INBOX.<Folder>` for servers using that prefix.
- TypeScript types throughout, `tsc --strict` clean.

### Security
- All MCP requests require `Authorization: Bearer <MCP_AUTH_TOKEN>`.
- Credentials never leave the server process — no telemetry, no third-party calls, no analytics.
- `.env.example` ships with placeholder values and an explicit instruction to generate a random `MCP_AUTH_TOKEN`.
- `nodemailer` pinned to `^8.0.7` (upstream patch for GHSA-mm7p-fcc7-pg87, GHSA-rcmh-qjqh-p98v, GHSA-c7w3-x93f-qmm8, GHSA-vvjj-xcjg-gr5g). `npm audit` clean on the shipped lockfile.

### Known limits (intentional in 1.0)
- Password / app-password auth only — no OAuth 2.0. Trades convenience on Gmail/iCloud for any-provider compatibility.
- HTTP transport only — no stdio mode.
- Custom Sent/Drafts/Trash folder names beyond the two fallback patterns require code changes.
- HTML body in `email_get_message` truncated past 25,000 characters.
- Binary attachments capped at 1 MB for base64 download.

---

## Roadmap (post-1.0, not committed)

- Optional `SENT_FOLDER` / `DRAFTS_FOLDER` / `TRASH_FOLDER` env overrides per account.
- OAuth flow for Gmail/Microsoft (separate add-on, not bundled).
- Webhook on incoming mail (push-style notifications for agents).
- Calendar/ICS bridge (separate product, will integrate cleanly).
- AI auto-tagging via the Anthropic API.

See [`README.md`](README.md) for the full project description.
