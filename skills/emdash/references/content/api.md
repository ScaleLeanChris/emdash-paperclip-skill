# Content API Reference

All REST endpoints are under `$EMDASH_URL/_emdash/api/`. MCP tools use the same auth.

## List Content

**MCP:** `content_list`
**REST:** `GET /content/{collection}`
**CLI:** `emdash content list <collection>`

Query params: `status`, `locale`, `orderBy`, `order`, `cursor`, `limit` (max 100, default 50)

```bash
curl -H "Authorization: Bearer $EMDASH_API_TOKEN" \
  "$EMDASH_URL/_emdash/api/content/posts?status=published&limit=10"
```

Response: `{ "items": [...], "nextCursor": "abc123" }`

## Get Content

**MCP:** `content_get`
**REST:** `GET /content/{collection}/{id}`
**CLI:** `emdash content get <collection> <id>`

The `{id}` can be an ID or a slug. Optional query: `locale`.

Response: `{ "item": { "id", "slug", "status", "data", "_rev", ... } }`

## Create Content

**MCP:** `content_create`
**REST:** `POST /content/{collection}`
**CLI:** `emdash content create <collection> --data '...'`

**Important:** Field names are schema-dependent. Always check the schema first:

```
schema_get_collection({ slug: "posts", includeFields: true })
```

Example (blog template with `title`, `content`, `excerpt`, `featured_image` fields):

```json
{
  "data": {
    "title": "My Post Title",
    "content": [
      {
        "_type": "block",
        "children": [{ "_type": "span", "text": "Hello world" }],
        "style": "normal"
      }
    ],
    "excerpt": "A brief summary of the post."
  },
  "slug": "my-post-title",
  "status": "draft",
  "locale": "en",
  "seo": {
    "metaTitle": "My Post | Blog",
    "metaDescription": "A great post about..."
  }
}
```

**Important:** Rich text fields (type `portableText`) must be **Portable Text** (JSON array of blocks), not HTML. See gotchas.md for format details.

If `slug` is omitted, it auto-generates from the title.

Response: `{ "item": { ... }, "_rev": "..." }` (status 201)

## Update Content

**MCP:** `content_update`
**REST:** `PUT /content/{collection}/{id}`
**CLI:** `emdash content update <collection> <id> --data '...' --rev <rev>`

```json
{
  "data": { "title": "Updated Title" },
  "_rev": "current-rev-token"
}
```

**The `_rev` field is required.** Get it from a prior `content_get`. If stale, you'll get a 409 Conflict — re-fetch and retry.

Partial updates are supported: only include fields you want to change.

## Delete Content (Soft)

**MCP:** `content_delete`
**REST:** `DELETE /content/{collection}/{id}`
**CLI:** `emdash content delete <collection> <id>`

Moves to trash. Recoverable via `content_restore`.

## Permanent Delete

**MCP:** `content_permanent_delete`
**REST:** `DELETE /content/{collection}/{id}/permanent`

**Irreversible.** Item must be trashed first. Requires ADMIN role.

## Restore from Trash

**MCP:** `content_restore`
**REST:** `POST /content/{collection}/{id}/restore`
**CLI:** `emdash content restore <collection> <id>`

## Publish

**MCP:** `content_publish`
**REST:** `POST /content/{collection}/{id}/publish`
**CLI:** `emdash content publish <collection> <id>`

Makes content live immediately. Syncs full-text search index.

## Unpublish

**MCP:** `content_unpublish`
**REST:** `POST /content/{collection}/{id}/unpublish`
**CLI:** `emdash content unpublish <collection> <id>`

Reverts to draft status. Content is no longer publicly visible.

## Schedule

**MCP:** `content_schedule`
**REST:** `POST /content/{collection}/{id}/schedule`
**CLI:** `emdash content schedule <collection> <id> --at <ISO8601>`

```json
{ "scheduledAt": "2026-04-15T09:00:00Z" }
```

Content auto-publishes at the scheduled time.

## Discard Draft

**MCP:** `content_discard_draft`
**REST:** `POST /content/{collection}/{id}/discard-draft`

Abandons draft changes and reverts to the live published version.

## Compare Live vs Draft

**MCP:** `content_compare`
**REST:** `GET /content/{collection}/{id}/compare`

Response: `{ "hasChanges": true, "live": { ... }, "draft": { ... } }`

## Duplicate

**MCP:** `content_duplicate`
**REST:** `POST /content/{collection}/{id}/duplicate`

Creates a copy as a new draft. Requires CONTRIBUTOR role.

## Translations

**MCP:** `content_translations`
**REST:** `GET /content/{collection}/{id}/translations`

Lists all locale variants in the same translation group.

## List Trashed

**MCP:** `content_list_trashed`
**REST:** `GET /content/{collection}/trash`

Query: `cursor`, `limit`

## Search

**MCP:** `search`
**REST:** `GET /search?q=<query>`
**CLI:** `emdash search <query>`

Optional: `collection`, `locale`, `limit`

## Revisions

**List:** `GET /content/{collection}/{id}/revisions` (MCP: `revision_list`)
**Restore:** `POST /revisions/{revisionId}/restore` (MCP: `revision_restore`)

Up to 50 revisions retained per item. Pruning is automatic.

## Comments (REST only)

```
GET  /comments/{collection}/{contentId}         # Public approved comments
GET  /admin/comments?status=pending              # Moderation inbox
GET  /admin/comments/counts                      # Status counts
PUT  /admin/comments/{id}/status                 # Approve/spam/trash
DELETE /admin/comments/{id}                       # Hard delete
POST /admin/comments/bulk                         # Bulk actions
     Body: { "ids": [...], "action": "approve|spam|trash|delete" }
```

## Set Taxonomy Terms on Content (REST only)

```
PUT /content/{collection}/{id}/terms/{taxonomy}
Body: { "termIds": ["term-id-1", "term-id-2"] }
```
