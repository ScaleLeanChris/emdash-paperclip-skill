# SEO Management

Manage meta tags, slugs, redirects, and structured data for search engine optimization.

## Reading Order

| Task | Start With | Then Read |
|------|-----------|-----------|
| Set meta tags on posts | api.md | patterns.md |
| Fix broken URLs / 404s | api.md (redirects) | gotchas.md |
| Improve search rankings | patterns.md | api.md |

## Key Concepts

- **SEO metadata** is set per-content-item via the `seo` field on content create/update
- **Slugs** are part of the content model, not a separate SEO system
- **Redirects** handle URL changes and fix 404s
- **Sitemap** is auto-generated from published content
- **No dedicated SEO MCP tools** — use content MCP tools for metadata, REST API for redirects

## Available Interfaces

| Operation | MCP Tool | REST Endpoint | CLI |
|-----------|----------|---------------|-----|
| Set SEO metadata | `content_create` / `content_update` (seo field) | Via content endpoints | Via content CLI |
| Manage slugs | `content_update` (slug field) | Via content endpoints | Via content CLI |
| List redirects | -- | `GET /redirects` | -- |
| Create redirect | -- | `POST /redirects` | -- |
| View 404 log | -- | `GET /redirects/404s` | -- |
| Sitemap data | -- | Via handler | -- |
