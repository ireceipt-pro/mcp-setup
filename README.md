# iReceipt.pro — MCP setup

Connect your AI assistant to your [iReceipt.pro](https://ireceipt.pro) account
through our hosted **Model Context Protocol (MCP)** server. iReceipt.pro renders
HTML (Mustache) templates into PDF, PNG, JPG and WEBP through a REST API; the MCP
server lets an assistant write and check those templates for you. No local
setup, no API keys — the server is remote and protected by OAuth 2.1, so you
sign in once with your iReceipt.pro account.

- **Server:** `ireceipt-pro`
- **Transport:** streamable HTTP
- **URL:** `https://mcp.ireceipt.pro/mcp`
- **Auth:** OAuth 2.1 (Authorization Code + PKCE) — you'll be prompted to sign in.

## What you can do

Once connected, the assistant can work with your **private templates** and
the **public gallery**, check a template before you use it, and render a preview:

| Tool | What it does |
|---|---|
| `list_templates` | List your private templates and/or the public gallery (ids, names, page sizes). |
| `get_template` | Fetch a template's HTML, sample arguments and page box. |
| `create_template` | Create a private template (within your plan's template limit). |
| `update_template` | Partially update a private template. |
| `delete_template` | Permanently delete a private template. |
| `validate_template` | Free static check: Mustache syntax, variables, and renderer traps (no scripts, no network). |
| `preview_template` | Render saved or unsaved HTML to an image. **Uses one request of your monthly quota.** |
| `get_template_guide` | The template authoring guide, by topic. |

---

## Install in Claude Code (plugin)

```
/plugin marketplace add ireceipt-pro/mcp-setup
/plugin install ireceipt-pro@ireceipt-pro
/reload-plugins
```

Then run `/mcp` and complete the sign-in when prompted.

> Prefer not to use the marketplace? Add the server directly:
> ```
> claude mcp add --transport http ireceipt-pro https://mcp.ireceipt.pro/mcp
> ```

---

## Connect from other clients

Same remote URL, same sign-in — only the config location differs.

### Claude Desktop / claude.ai connectors

1. Open **Settings → Connectors → Add custom connector**.
2. Name: `iReceipt.pro`
3. URL: `https://mcp.ireceipt.pro/mcp`
4. Save, then connect and sign in when prompted.

### Cursor

`~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (per project):

```json
{
  "mcpServers": {
    "ireceipt-pro": {
      "url": "https://mcp.ireceipt.pro/mcp"
    }
  }
}
```

### VS Code (GitHub Copilot / MCP) and other clients

```json
{
  "servers": {
    "ireceipt-pro": {
      "type": "http",
      "url": "https://mcp.ireceipt.pro/mcp"
    }
  }
}
```

---

## Authentication

The server implements OAuth 2.1 with:

- Authorization Code grant + **PKCE (S256)**, refresh tokens.
- Dynamic Client Registration (RFC 7591) — clients register automatically.
- Scopes: `templates:read`, `templates:write`, `templates:render`, `offline_access`.
- Discovery documents:
  - `https://mcp.ireceipt.pro/.well-known/oauth-protected-resource`
  - `https://mcp.ireceipt.pro/.well-known/oauth-authorization-server`
  - `https://mcp.ireceipt.pro/.well-known/openid-configuration`

Consent is shown on [dashboard.ireceipt.pro](https://dashboard.ireceipt.pro).
Tokens are issued to your account — the assistant only sees and changes **your**
templates. To disconnect an assistant, open **Agents** in the dashboard and
press **Forget**.

Guided **prompts** (`create_template`, `fix_template`, `template_guide`) and the
`template-guide` resource are exposed too, so the assistant finds the right
workflow on its own.

---

## Troubleshooting

- **`401 Unauthorized` / asked to sign in again** — the access token expired.
  Re-run the client's connect/authorize action.
- **Tools don't appear** — confirm the client finished the OAuth flow (in Claude
  Code, `/mcp` should show the server as *connected*).
- **A preview failed with a quota message** — `preview_template` counts against
  your plan; `validate_template` is free and catches most problems first.
- **Wrong account** — disconnect in the dashboard (**Agents → Forget**) and
  connect again.

## Support

- Website: https://ireceipt.pro
- Dashboard: https://dashboard.ireceipt.pro
- API docs: https://api.ireceipt.pro
- Support: support@ireceipt.pro
