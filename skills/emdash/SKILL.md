---
name: emdash
description: Manage an emdash CMS blog deployed on Cloudflare — create, edit, publish, and schedule content, upload media, manage SEO metadata, organize taxonomies, plan content strategy, and distribute across channels. Use when the agent needs to work with emdash, blog management, content publishing, CMS operations, or when tasks mention "emdash," "blog post," "publish article," "content calendar," "editorial," "media upload," "SEO metadata," "categories," "tags," "blog strategy," "content schedule," "draft," "revision," or "blog analytics." For Cloudflare deployment of the emdash instance itself, see cloudflare-deploy.
metadata:
  version: 1.0.0
---

# EmDash CMS

Skill for managing an emdash blog. Covers content lifecycle, media, SEO, taxonomies, content strategy, and distribution.

## Prerequisites

Verify these env vars are set before any operation:

- `EMDASH_URL` — Base URL of the emdash instance (e.g., `https://blog.example.com`)
- `EMDASH_API_TOKEN` — Personal Access Token (`ec_pat_*`) with appropriate scopes

Test connectivity:

```bash
curl -s -H "Authorization: Bearer $EMDASH_API_TOKEN" "$EMDASH_URL/_emdash/api/auth/me"
```

For full connection setup (MCP, REST, CLI), see `references/connection.md`.

## Interface Priority

1. **MCP server** (preferred) — 26 tools at `$EMDASH_URL/_emdash/api/mcp`, stateless HTTP transport
2. **REST API** (fallback) — ~120 endpoints under `$EMDASH_URL/_emdash/api/`
3. **CLI** (fallback) — `emdash` command with `--url` and `--token` flags

MCP covers content, schema, media, search, taxonomy, menus, and revisions. For SEO metadata, comments, redirects, sections, widgets, and settings — use the REST API.

## Quick Decision Trees

### "I need to work with content"

```
Content task?
├─ Create / edit / delete posts       → references/content/
├─ Publish / unpublish / schedule     → references/content/
├─ Manage revisions / drafts          → references/content/
├─ Compare live vs draft              → references/content/
├─ Duplicate or translate content     → references/content/
└─ Full-text search                   → references/content/
```

### "I need to work with media"

```
Media task?
├─ Upload images / files              → references/media/
├─ Update alt text / captions         → references/media/
├─ List / browse media library        → references/media/
└─ Delete media files                 → references/media/
```

### "I need to optimize for search"

```
SEO task?
├─ Set meta title / description       → references/seo/
├─ Manage slugs                       → references/seo/
├─ Configure sitemap                  → references/seo/
├─ Set up redirects / fix 404s        → references/seo/
└─ Add structured data                → references/seo/
```

### "I need to organize content"

```
Taxonomy task?
├─ Create / manage categories         → references/taxonomy/
├─ Create / manage tags               → references/taxonomy/
├─ Build hierarchical taxonomies      → references/taxonomy/
├─ Assign terms to content            → references/taxonomy/
└─ Manage navigation menus            → references/taxonomy/
```

### "I need to plan what to write"

```
Strategy task?
├─ Build content calendar             → references/strategy/
├─ Research topics / keywords         → references/strategy/
├─ Plan editorial cadence             → references/strategy/
├─ Analyze content gaps               → references/strategy/
└─ Define content pillars             → references/strategy/
```

### "I need to promote content"

```
Distribution task?
├─ Create social media posts          → references/distribution/
├─ Draft newsletter content           → references/distribution/
├─ Cross-promote across channels      → references/distribution/
└─ Repurpose content for platforms    → references/distribution/
```

## Domain Index

| Domain | MCP Tools | REST API | CLI | Reference |
|--------|:---------:|:--------:|:---:|-----------|
| Content | 15 tools | Full CRUD + workflow | `emdash content` | `references/content/` |
| Media | 4 tools | Full CRUD + upload | `emdash media` | `references/media/` |
| SEO | -- | Via content + redirects | -- | `references/seo/` |
| Taxonomy | 3 tools | Full CRUD | `emdash taxonomy` | `references/taxonomy/` |
| Strategy | -- | -- | -- | `references/strategy/` |
| Distribution | -- | -- | -- | `references/distribution/` |
| Schema | 6 tools | Full CRUD | `emdash schema` | `references/connection.md` |
| Search | 1 tool | Full-text + rebuild | `emdash search` | `references/content/` |
| Menus | 2 tools | Full CRUD | `emdash menu` | `references/taxonomy/` |
| Revisions | 2 tools | List + restore | -- | `references/content/` |
| Comments | -- | Admin moderation | -- | `references/content/` |
| Settings | -- | Get / update | -- | `references/connection.md` |

## Content Model

EmDash uses a database-first schema. Each collection gets a real SQL table (`ec_*`) with standard columns:

- **Core:** `id`, `slug`, `status`, `author_id`, `created_at`, `updated_at`
- **Publishing:** `published_at`, `scheduled_at`, `deleted_at`
- **Revisions:** `live_revision_id`, `draft_revision_id`
- **Localization:** `locale`, `translation_group`

Rich text is stored as **Portable Text** (JSON), not HTML. This is critical — never send raw HTML to content fields. Use the TipTap/Portable Text format.

**Important:** Field names are schema-dependent. Always call `schema_get_collection` with `includeFields: true` before creating content to discover the correct field names (e.g., the rich text field might be `content`, `body`, or `text`).

## RBAC Roles

| Role | Level | Can Do |
|------|-------|--------|
| SUBSCRIBER | 10 | Read content, media, taxonomies |
| CONTRIBUTOR | 20 | Create content, upload media |
| AUTHOR | 30 | Edit/publish own content |
| EDITOR | 40 | Edit/publish any content, manage taxonomies |
| ADMIN | 50 | Everything: schema, users, settings, plugins |

## What Agents Can and Cannot Change

Agents control **content and structure** (data layer), not **visual design** (code layer):

| Can do via API | Cannot do via API |
|---------------|-------------------|
| Site title and tagline (`PUT /settings`) | Change CSS, colors, fonts, spacing |
| Reusable sections (hero, CTA blocks) | Switch templates (blog → portfolio) |
| Widget areas and widgets (sidebar, footer) | Modify component layout or header style |
| Navigation menus | Install or change themes |
| Content schema (add fields, collections) | Edit Astro template code |

Design changes require modifying the Astro source code in the site repository and redeploying. This is by design — emdash treats visual design as code and content as data.

## Troubleshooting

- **401 Unauthorized:** Token expired or missing scopes. Check `emdash whoami`.
- **409 Conflict:** Stale `_rev` token on update. Re-fetch the item, get current `_rev`, retry.
- **Portable Text errors:** You sent HTML instead of Portable Text JSON. Convert first.
- **MCP connection fails:** Fall back to REST API. MCP requires authenticated session.
