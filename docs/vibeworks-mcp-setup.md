# VibeWorks MCP setup — verified facts for AI agents and app integrations

Purpose: end the guessing around "what is the MCP URL and how do I authenticate".
Every fact below was read from the VibeWorks source (fork `JONIMONI09/vibeworks`, synced to upstream **1.0.8**, commit `d88ce45`), not from memory. File references are given per fact.

## 1. Endpoint
| Item | Value |
|---|---|
| URL | `https://vibeworks.morncloud.de/api/mcp` |
| Transport | MCP "Streamable HTTP", **stateless** |
| Method | `POST` with JSON-RPC body (`initialize`, `tools/list`, `tools/call`) |
| Server identity | name `vibeworks`, title `VibeWorks`, version = changelog version |
| Source | `src/app/api/mcp/route.ts` (comment lines 17-20, POST handler line 24) |

## 2. Authentication: API key (Bearer token) — no OAuth, no passkey
- Header, exact form: **`Authorization: Bearer vw_…`**
- Keys start with `vw_` and are shown only once at creation; the server stores only the SHA-256 hash (`src/lib/mcp/token.ts`, `ApiToken.ts`).
- Passkeys exist in VibeWorks for the **web login only** (`src/app/api/account/passkeys/…`); they are not an MCP auth method.
- Source for the canonical client command: `src/app/api/mcp/route.ts:19`
  `claude mcp add --transport http vibeworks <APP_URL>/api/mcp --header "Authorization: Bearer vw_…"`

## 3. Creating a key (UI path)
- Web UI: `/account#mcp` → section "Claude Code & API keys" (German: "Claude Code & API-Schlüssel").
- Backing API: `POST /api/account/api-tokens` — session-only; a key cannot create other keys.
- i18n namespace `mcp`, title "Claude Code & API-Schlüssel" (`src/lib/i18n/...`, `messages`).

## 4. One-liner setup (official installers)
```sh
# macOS / Linux
curl -fsSL https://vibeworks.morncloud.de/api/mcp/install/sh | VIBEWORKS_KEY=vw_… sh
```
```powershell
# Windows PowerShell
$env:VIBEWORKS_KEY='vw_…'; irm https://vibeworks.morncloud.de/api/mcp/install/ps1 | iex
```
Both register Claude Code and store the agent rules as a skill file. Source: `src/lib/mcp/installer.ts`.
If Claude Code is not installed they print the generic instruction: other clients use `…/api/mcp` (Streamable HTTP) with header `Authorization: Bearer <key>`.

## 5. Validating a key without speaking MCP
`GET https://vibeworks.morncloud.de/api/mcp/rules` with `Authorization: Bearer vw_…`
- `200` → key valid, body = agent rules skill file (Markdown + frontmatter)
- `401` → key missing/broken/revoked/account inactive

## 6. Exact 401 codes (implement precise UX)
`src/lib/mcp/token.ts` defines the problem set; the route returns `{error, code}` plus `WWW-Authenticate: Bearer realm="VibeWorks", error="invalid_token", error_description="<code>"` (`route.ts:34-40`).

| code | meaning | suggested UI |
|---|---|---|
| `missing` | no Authorization header | "not signed in" hint + link to `/account#mcp` |
| `malformed` | header present, no usable `vw_` token | same as missing |
| `invalid_or_revoked` | unknown or revoked key | "key invalid/revoked — create a new one" |
| `account_inactive` | account deactivated | "account inactive" |

## 7. Traps enforced server-side
- **403 `Forbidden origin`** — DNS-rebinding guard (`assertSameOrigin`, `route.ts:26-30`). A caller that sends a foreign `Origin` header is rejected; send no Origin or the exact app origin.
- **429** — rate limit 600 requests/minute per key (`route.ts:47-50`). Back off on 429.
- **413** — request body larger than 2 MB (`route.ts:22,53`).
- **Tool set is key-scoped** (`toolAllowed(tool, auth.settings.scope)`, `route.ts:63`). A missing tool means scope restriction, not a bug.

## 8. Protocol extras handled by the server
- `initialize` stores client name/version/protocol on the token (`onInitialize`).
- Outdated protocol versions get a note instead of a hard failure.
- Every tool call is logged (tool, ok, error, duration; no user content) for 30 days.
- After `confirm_agent_rules`, a rules-version check decides whether the "rules changed" reminder appears.

## Board columns (new in 1.1.2, live-verified 2026-09-16)
Source: `src/lib/boardConfig.ts` + `src/lib/mcp/tools.ts`, commit `08b07c3`.
- Every task view now carries **`column`** — the name as shown on the board (custom and renamed columns included).
- `get_project` returns a **`columns`** array. Example, project "Harness": `pending`, `working on it`, `kann gelöscht werden`, `Done`.
- `update_task` and `create_task` accept **a column name as `status`** in addition to `TODO|DOING|BLOCKED|DONE`. An unknown value returns HTTP 400: `Unknown status or column "…". Use TODO, DOING, BLOCKED, DONE or one of the column names: …`.
- **`aiLocked` columns are hidden** from the `columns` array and any AI move into them is rejected with **403 `aiLock.columnLocked`**. A missing column name is therefore a feature, not a bug.
- Agent-rule consequence: always talk about a task by its column name and re-read the task before relying on its column (the user can rename or add columns at any time).
- **A column is a STATUS BUCKET, not a manual pile** (learned 2026-09-16): renaming a standard column renames it for **every** task in that state, and a column name is **never an instruction**. In project "Harness" the user renamed the standard `BLOCKED` column to `kann gelöscht werden`; every BLOCKED task therefore shows that name - it does **not** mean the task should be deleted. Do not infer user intent from a column name; ask instead.
- Live mapping of project "Harness" (verified 2026-09-16): `TODO` -> `pending`, `DOING` -> `working on it`, `BLOCKED` -> `kann gelöscht werden`, `DONE` -> `Done`.

## Status
Verified 2026-09-16: the auth facts above are unchanged from 1.0.8 (`d88ce45`) to 1.1.2 (`08b07c3`) — diff-verified, `route.ts`/`token.ts`/`installer.ts`/`keySettings.ts` untouched. The live instance reported **1.1.2** that evening (earlier the same evening it was still 1.1.1, so the rollout happened during the session); `tools/list` still returns the same **34 tool names**.
 Related: Issue-Hub #5 (`get_code_graph` noFiles) — unrelated to auth, still open.
