# OutSend MCP server

OutSend is a B2B lead generation service. Its remote MCP server lets an AI assistant extract local businesses, enrich them with emails, socials and reviews, verify emails, and chain these steps into pipelines and recurring monitors.

This repository holds documentation and public metadata for the hosted server. It contains no source code: the server runs at `https://outsend.xyz/mcp` and there is nothing to install.

| | |
|---|---|
| Endpoint | `https://outsend.xyz/mcp` |
| Transport | Streamable HTTP (stateless) |
| Authentication | OAuth 2.1, or a personal API key |
| Official MCP Registry | `xyz.outsend/outsend` |
| Documentation | https://outsend.xyz/docs/integration/mcp |
| Sign up | https://outsend.xyz/join/mcp |

## Authentication

Two methods are supported.

**OAuth 2.1.** Authorization code with PKCE (S256), dynamic client registration and client ID metadata documents. A request without credentials gets `401 Unauthorized` with a `WWW-Authenticate` header pointing to `https://outsend.xyz/.well-known/oauth-protected-resource/mcp`, which is how MCP clients discover the flow. Scopes: `mcp`, `offline_access`. Access tokens last one hour and are renewed automatically. To disconnect a client, revoke the app in OutSend under Settings → API keys → Connected apps.

**API key.** Create a key in OutSend under Settings → API keys and send it as `Authorization: Bearer osk_...`. If a key leaks, revoke it in the same place.

No account yet: sign up at https://outsend.xyz/join/mcp.

## Connect a client

In every block below, replace `osk_...` with your own key. Menu names are those of each client at the time of writing and may change.

### Claude (claude.ai and Claude Desktop)

1. Open Settings → Connectors.
2. Click Add custom connector.
3. URL: `https://outsend.xyz/mcp`. Leave the OAuth Client ID and Client Secret fields empty: Claude registers itself automatically.
4. Click Connect, sign in to OutSend if asked, then click Authorize.

### ChatGPT (developer mode)

1. In ChatGPT settings, enable developer mode.
2. Create a new connector (app) and set the MCP server URL to `https://outsend.xyz/mcp`.
3. Choose OAuth as the authentication method, then sign in to OutSend and authorize.

### Claude Code

```bash
claude mcp add outsend --transport http https://outsend.xyz/mcp \
  --header "Authorization: Bearer osk_..."
```

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "outsend": {
      "url": "https://outsend.xyz/mcp",
      "headers": { "Authorization": "Bearer osk_..." }
    }
  }
}
```

### VS Code

`.vscode/mcp.json` in your workspace:

```json
{
  "servers": {
    "outsend": {
      "type": "http",
      "url": "https://outsend.xyz/mcp",
      "headers": { "Authorization": "Bearer osk_..." }
    }
  }
}
```

Keep the file out of version control once it holds a real key.

## Tools

The server exposes 13 tools. The list below comes from the public server card, https://outsend.xyz/.well-known/mcp/server-card.json, also copied in this repository as `server-card.json` with the full descriptions and input schemas.

| Name | Title | Access |
|---|---|---|
| `list_modules` | List modules | Read |
| `get_pipeline_schema` | Get pipeline schema | Read |
| `estimate_scrap` | Estimate scrape cost | Read |
| `create_scrap_job` | Create scrape job | Write |
| `create_enrichment_job` | Create enrichment job | Write |
| `get_job` | Get job | Read |
| `get_job_results` | Get job results | Read |
| `list_jobs` | List jobs | Read |
| `create_pipeline` | Create and start pipeline | Write |
| `list_pipelines` | List pipelines | Read |
| `create_monitor` | Create monitor | Write |
| `list_monitors` | List monitors | Read |
| `get_events` | Get events | Read |

Jobs are asynchronous: the assistant creates a job, polls `get_job`, then fetches a preview with `get_job_results`. Full data always goes through the download URL, never inline in the chat.

## Scope and limits

The MCP server inherits the account's normal permissions, whether the call carries an OAuth token or an API key.

- It cannot access other users' data.
- The same quotas as the web app apply: an over-quota pipeline is refused in the same way.
- It cannot touch billing, account settings or admin surfaces, and it cannot manage API keys.
- OAuth tokens only work on `/mcp`: they do not open the REST API.
- Rate limit: 600 MCP calls per hour per account.

## Files

- `server.json`: the entry published in the official MCP Registry under `xyz.outsend/outsend`.
- `server-card.json`: copy of the public server card served at `https://outsend.xyz/.well-known/mcp/server-card.json`.

## Links

- Documentation: https://outsend.xyz/docs/integration/mcp
- Sign up: https://outsend.xyz/join/mcp
- Privacy policy: https://outsend.xyz/en/confidentialite
- Terms: https://outsend.xyz/en/conditions
- Contact: contact@outsend.xyz

## License

Copyright (c) 2026 OutSend. All rights reserved. See `LICENSE`.
