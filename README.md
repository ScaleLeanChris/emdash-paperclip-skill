# emdash — Paperclip Agent Skill

A [Paperclip](https://paperclip.ai) agent skill that gives any agent full control over an [emdash CMS](https://github.com/emdash-cms/emdash) blog deployed on Cloudflare.

## What it does

Assign this skill to any Paperclip agent and it can:

- **Create, edit, publish, and schedule** blog posts using Portable Text
- **Upload and manage media** (images, files) on Cloudflare R2
- **Optimize SEO** — meta tags, slugs, redirects, 404 monitoring
- **Organize content** with categories, tags, custom taxonomies, and navigation menus
- **Plan content strategy** — editorial calendars, topic research, content audits
- **Distribute content** across social media, newsletters, and messaging platforms

## How agents connect to emdash

The skill supports three interfaces, in order of preference:

| Interface | Coverage | Best for |
|-----------|----------|----------|
| **MCP server** | 26 tools (content, schema, media, search, taxonomy, menus, revisions) | Primary operations |
| **REST API** | ~120 endpoints (full coverage including SEO, comments, redirects, settings) | Operations not available via MCP |
| **CLI** | Content, media, schema, taxonomy, search, menus | Scripted workflows |

## Installation

### 1. Clone into your local companies directory

```bash
cd ~/.paperclip/local-companies
git clone https://github.com/ScaleLeanChris/emdash-paperclip-skill.git emdash-skills
```

### 2. Get an emdash API token

In your emdash admin panel, go to **Settings → API Tokens** and create a Personal Access Token with these scopes:

| Scope | Required | Why |
|-------|----------|-----|
| `content:read` | Yes | List and read blog posts |
| `content:write` | Yes | Create, edit, publish, schedule posts |
| `media:read` | Yes | Browse media library |
| `media:write` | Yes | Upload images and files |
| `schema:read` | Recommended | Discover collection field names before writing |
| `admin` | Optional | Full access (settings, users, plugins) |

The token will look like `ec_pat_...` — save it, you'll only see it once.

### 3. Configure agent environment variables

After importing a company that uses this skill (or when creating/updating an agent), set the environment variables via the Paperclip API:

```bash
# Find the agent ID
curl http://127.0.0.1:3100/api/companies/{companyId}/agents

# Set the env vars on each agent that uses the emdash skill
curl -X PATCH "http://127.0.0.1:3100/api/agents/{agentId}" \
  -H "Content-Type: application/json" \
  -d '{
    "adapterConfig": {
      "env": {
        "EMDASH_URL": "https://your-emdash-instance.example.com",
        "EMDASH_API_TOKEN": "ec_pat_your_token_here"
      }
    }
  }'
```

**Note:** There is currently no UI for setting agent environment variables. Use the REST API as shown above. The env vars are stored encrypted in the Paperclip database and injected into the agent's process at runtime.

### 4. Assign the skill to an agent

Add `"emdash"` to the agent's `skills` array in the manifest or via the API:

```json
{
  "slug": "my-blog-agent",
  "skills": ["emdash"],
  "adapterConfig": {
    "env": {
      "EMDASH_URL": "https://blog.example.com",
      "EMDASH_API_TOKEN": "ec_pat_..."
    }
  }
}
```

### 5. Verify the connection

The agent will automatically test connectivity on first use. You can also verify manually:

```bash
curl -s -H "Authorization: Bearer $EMDASH_API_TOKEN" \
  "$EMDASH_URL/_emdash/api/auth/me"
```

## Skill structure

```
skills/emdash/
├── SKILL.md                          # Decision tree + domain routing (149 lines)
├── agents/openai.yaml                # UI metadata
└── references/
    ├── connection.md                 # Auth, MCP setup, REST API, CLI, token scopes
    ├── content/                      # Content CRUD, publishing, revisions, comments
    │   ├── README.md                 #   Reading order + interface matrix
    │   ├── api.md                    #   All content endpoints with examples
    │   ├── patterns.md               #   Workflows: draft→publish, scheduling, i18n
    │   └── gotchas.md                #   Portable Text format, _rev conflicts, field names
    ├── media/                        # Image/file upload and management
    │   ├── README.md
    │   ├── api.md                    #   Upload (REST only), list, update, delete
    │   ├── patterns.md               #   Upload→embed workflow, alt text, batch upload
    │   └── gotchas.md                #   MCP can't upload, deduplication, permanent delete
    ├── seo/                          # Search engine optimization
    │   ├── README.md
    │   ├── api.md                    #   SEO metadata, redirects, 404 log, sitemap
    │   ├── patterns.md               #   On-page checklist, slug changes, content freshness
    │   └── gotchas.md                #   No dedicated MCP tools, meta vs content title
    ├── taxonomy/                     # Categories, tags, menus
    │   ├── README.md
    │   ├── api.md                    #   Term CRUD, content assignment, menu management
    │   ├── patterns.md               #   Blog taxonomy setup, navigation, topic clusters
    │   └── gotchas.md                #   Term assignment replaces, deletion cascades
    ├── strategy/                     # Content planning (no API — workflow-driven)
    │   ├── README.md
    │   ├── patterns.md               #   Content audit, pillars, calendar, topic research
    │   └── gotchas.md                #   Over-scheduling, cannibalization, timezone
    └── distribution/                 # Multi-channel promotion
        ├── README.md
        ├── patterns.md               #   Post-publish workflow, templates, repurposing
        └── gotchas.md                #   URL construction, channel config, character limits
```

## What emdash is

[EmDash](https://github.com/emdash-cms/emdash) is a modern, full-stack CMS built on Astro and Cloudflare. It uses D1 (SQLite) for the database, R2 for media storage, and Workers for compute. Content is stored as Portable Text (JSON), not HTML, which makes it ideal for multi-channel distribution.

## Key things agents need to know

1. **Always check the schema first.** Field names vary by collection. Call `schema_get_collection({ slug: "posts", includeFields: true })` before creating content.
2. **Use Portable Text, not HTML.** All rich text fields are JSON arrays of blocks with `_type`, `style`, and `children`.
3. **Include `_rev` on updates.** EmDash uses optimistic concurrency — stale `_rev` tokens cause 409 conflicts.
4. **MCP can't upload media.** Use the REST API (`POST /media`) or CLI (`emdash media upload`) for file uploads.

## Compatibility

- **Paperclip:** Any version with skill import support
- **EmDash:** v0.1.0+
- **Deployment:** Cloudflare (D1 + R2 + Workers) or self-hosted (SQLite + local storage)

## Updating

Skills are not auto-updated after import. To get the latest version:

```bash
cd ~/.paperclip/local-companies/emdash-skills
git pull
npx paperclipai company import --from ~/.paperclip/local-companies/emdash-skills --collision replace
```

This pulls the latest changes from GitHub and re-imports with updated skill files. Your agent configurations (env vars, assignments) are preserved — only skill content is replaced.

## Related

- [emdash-blog-team](https://github.com/ScaleLeanChris/emdash-blog-team) — Ready-to-import 5-agent company that uses this skill (CEO, Website Manager, Content Writer, SEO Specialist, Distribution Manager)
- [emdash CMS](https://github.com/emdash-cms/emdash) — The CMS itself
- [Paperclip](https://paperclip.ai) — Multi-agent coordination platform
- [paperclip.space](https://paperclip.space) — Paperclip community hub, where this package is listed

## License

MIT
