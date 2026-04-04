# Connection & Authentication

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| `EMDASH_URL` | Yes | Base URL (e.g., `https://blog.example.com`) |
| `EMDASH_API_TOKEN` | Yes | Personal Access Token (`ec_pat_*`) |

### Setting env vars in Paperclip

Set these on each agent via the Paperclip API using `adapterConfig.env`:

```bash
curl -X PATCH "http://127.0.0.1:3100/api/agents/{agentId}" \
  -H "Content-Type: application/json" \
  -d '{
    "adapterConfig": {
      "env": {
        "EMDASH_URL": "https://your-blog.example.com",
        "EMDASH_API_TOKEN": "ec_pat_your_token_here"
      }
    }
  }'
```

Values are stored encrypted and injected into the agent's process at runtime.

## MCP Server (Preferred)

EmDash exposes an MCP server at `$EMDASH_URL/_emdash/api/mcp` using Streamable HTTP transport (stateless, no session management).

### Connecting

The MCP server authenticates via the same Bearer token as the REST API. Configure your MCP client:

```json
{
  "mcpServers": {
    "emdash": {
      "url": "${EMDASH_URL}/_emdash/api/mcp",
      "headers": {
        "Authorization": "Bearer ${EMDASH_API_TOKEN}"
      }
    }
  }
}
```

### Available MCP Tools (26)

| Category | Tools | Count |
|----------|-------|-------|
| Content | `content_list`, `content_get`, `content_create`, `content_update`, `content_delete`, `content_restore`, `content_permanent_delete`, `content_publish`, `content_unpublish`, `content_schedule`, `content_compare`, `content_discard_draft`, `content_list_trashed`, `content_duplicate`, `content_translations` | 15 |
| Schema | `schema_list_collections`, `schema_get_collection`, `schema_create_collection`, `schema_delete_collection`, `schema_create_field`, `schema_delete_field` | 6 |
| Media | `media_list`, `media_get`, `media_update`, `media_delete` | 4 |
| Search | `search` | 1 |
| Taxonomy | `taxonomy_list`, `taxonomy_list_terms`, `taxonomy_create_term` | 3 |
| Menus | `menu_list`, `menu_get` | 2 |
| Revisions | `revision_list`, `revision_restore` | 2 |

**Not available via MCP** (use REST API): SEO metadata, comments, redirects, sections, widgets, settings, media upload, user management, plugins.

## REST API (Fallback)

All endpoints live under `$EMDASH_URL/_emdash/api/`. Authenticate with Bearer token:

```bash
curl -H "Authorization: Bearer $EMDASH_API_TOKEN" \
     -H "Content-Type: application/json" \
     "$EMDASH_URL/_emdash/api/content/posts"
```

### Response Format

Success: `{ "data": { ... } }` or `{ "items": [...], "nextCursor": "..." }`
Error: `{ "error": { "code": "NOT_FOUND", "message": "..." } }`

### Pagination

Cursor-based. Pass `?cursor=<nextCursor>&limit=50` (max 100, default 50).

## CLI (Fallback)

Install: `npm install -g emdash` (or use `npx emdash`).

```bash
emdash --url "$EMDASH_URL" --token "$EMDASH_API_TOKEN" content list posts
```

Or authenticate persistently:

```bash
emdash login --url "$EMDASH_URL"  # OAuth Device Flow
emdash whoami                      # Verify
```

Credentials stored at `~/.config/emdash/auth.json`.

## Token Scopes

| Scope | Min Role | Grants |
|-------|----------|--------|
| `content:read` | SUBSCRIBER | Read content, taxonomies, menus |
| `content:write` | CONTRIBUTOR | Create/edit/publish content |
| `media:read` | SUBSCRIBER | Browse media library |
| `media:write` | CONTRIBUTOR | Upload/edit/delete media |
| `schema:read` | EDITOR | Read collection definitions |
| `schema:write` | ADMIN | Create/modify collections |
| `admin` | ADMIN | Full access (wildcard) |

**Recommendation:** For blog management agents, create a token with `content:read`, `content:write`, `media:read`, `media:write` scopes at EDITOR role level.

## Site Settings (REST only)

```bash
# Get settings
GET /_emdash/api/settings

# Update settings
PUT /_emdash/api/settings
Body: { "siteName": "My Blog", "siteDescription": "...", ... }
```

## OpenAPI Spec

Available at `$EMDASH_URL/_emdash/api/openapi.json` for programmatic API discovery.
