# SEO API Reference

## SEO Metadata (via Content API)

SEO metadata is a field on content items. Set it during create or update:

```json
{
  "data": { "title": "My Post", "body": [...] },
  "seo": {
    "metaTitle": "My Post Title | Blog Name",
    "metaDescription": "A 155-character description for search results..."
  }
}
```

**MCP:** Include `seo` in `content_create` or `content_update` calls.
**REST:** Include `seo` in POST/PUT body to content endpoints.

## Slug Management

Slugs are set on the content item directly:

```json
{
  "slug": "my-custom-url-path"
}
```

When changing a slug on a published post, create a redirect from the old URL to preserve SEO value.

## Redirects (REST only)

### List Redirects

```
GET /redirects?cursor=&limit=50&search=&group=&enabled=true&auto=
```

Response: `{ "items": [{ "id", "source", "destination", "type", "enabled", "groupName", "hits", "lastHitAt" }], "nextCursor": "..." }`

### Create Redirect

```
POST /redirects
Body: {
  "source": "/old-path",
  "destination": "/new-path",
  "type": 301,
  "enabled": true,
  "groupName": "slug-changes"
}
```

Redirect types:
- `301` — Permanent redirect (passes SEO value, use for slug changes)
- `302` — Temporary redirect (does not pass SEO value)

### Update Redirect

```
PUT /redirects/{id}
Body: { "source": "/updated-old-path", "destination": "/updated-new-path", "type": 301, "enabled": true }
```

### Delete Redirect

```
DELETE /redirects/{id}
```

## 404 Log (REST only)

### List 404s

```
GET /redirects/404s?cursor=&limit=50&search=
```

Shows paths that returned 404 errors with timestamps and referrers.

### 404 Summary

```
GET /redirects/404s/summary?limit=20
```

Groups 404s by path and shows hit counts. Use this to identify the most impactful broken URLs to fix.

## Sitemap Data

EmDash auto-generates sitemap data from all published content. The sitemap handler returns:

```json
{
  "entries": [
    { "collection": "posts", "identifier": "my-post", "updatedAt": "2026-04-01T..." },
    ...
  ]
}
```

This feeds into the `sitemap.xml` route automatically. No manual management needed.

## Full-Text Search

```
GET /search?q=keyword&collection=posts&limit=10
```

**MCP:** `search({ query: "keyword", collection: "posts" })`

Search index updates automatically on publish/unpublish. Force rebuild with:

```
POST /search/rebuild  (ADMIN only)
```
