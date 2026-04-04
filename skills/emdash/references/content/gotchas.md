# Content Gotchas

## Field Names Are Schema-Dependent

Content field names come from the collection schema, not from a fixed convention. **Always check the schema first:**

```
schema_get_collection({ slug: "posts", includeFields: true })
```

Common field names by template:
- Blog template: `title`, `content` (portableText), `featured_image`, `excerpt`
- Other templates may differ

**Never assume `body`** — the field might be `content`, `text`, `description`, or anything else defined in the schema.

## Portable Text Format

EmDash stores rich text as **Portable Text** (JSON), not HTML. This is the most common source of errors.

### Correct format

```json
[
  {
    "_type": "block",
    "style": "normal",
    "children": [
      { "_type": "span", "text": "This is a paragraph." }
    ]
  },
  {
    "_type": "block",
    "style": "h2",
    "children": [
      { "_type": "span", "text": "A Heading" }
    ]
  },
  {
    "_type": "block",
    "style": "normal",
    "children": [
      { "_type": "span", "text": "Text with " },
      { "_type": "span", "text": "bold", "marks": ["strong"] },
      { "_type": "span", "text": " formatting." }
    ]
  }
]
```

### Common styles

| Style | Renders As |
|-------|-----------|
| `normal` | Paragraph `<p>` |
| `h1` through `h6` | Headings |
| `blockquote` | Block quote |

### Common marks

| Mark | Effect |
|------|--------|
| `strong` | **Bold** |
| `em` | *Italic* |
| `underline` | Underline |
| `strike-through` | ~~Strikethrough~~ |
| `code` | `Inline code` |

### Links in Portable Text

```json
{
  "_type": "block",
  "children": [
    { "_type": "span", "text": "Click " },
    {
      "_type": "span",
      "text": "here",
      "marks": ["link-1"]
    }
  ],
  "markDefs": [
    { "_key": "link-1", "_type": "link", "href": "https://example.com" }
  ]
}
```

### Images in Portable Text

```json
{
  "_type": "image",
  "mediaId": "media-item-id",
  "alt": "Description of image"
}
```

Upload the image via the media API first, then reference its ID.

## Optimistic Concurrency (`_rev`)

Every content item has a `_rev` token. Updates **require** the current `_rev`:

```json
{ "data": { "title": "New Title" }, "_rev": "abc123" }
```

If someone else edited the item since you fetched it, you get **409 Conflict**. Resolution:

1. Re-fetch the item to get current `_rev` and content
2. Merge your changes with the current content
3. Retry the update with the new `_rev`

## Revision Limits

EmDash retains up to **50 revisions** per content item. Older revisions are pruned automatically in the background. You cannot change this limit.

## Slug Auto-Generation

If you omit `slug` on create, emdash generates one from the title. If the slug conflicts, it appends a suffix (e.g., `my-post-1`). You can set a custom slug explicitly.

## Soft Delete vs Hard Delete

- `content_delete` → moves to trash (recoverable)
- `content_permanent_delete` → irreversible, requires ADMIN role, item must be trashed first

Always prefer soft delete unless explicitly asked to permanently remove content.

## Status Values

| Status | Meaning |
|--------|---------|
| `draft` | Not published, only visible in admin |
| `published` | Live and publicly visible |
| `scheduled` | Will auto-publish at `scheduledAt` time |
| `trashed` | Soft-deleted, recoverable |

## Search Index Sync

Publishing or unpublishing content automatically syncs the full-text search index. If search results seem stale, you can force a rebuild:

```
POST /search/rebuild
```

This is an admin-only operation.

## Publish Requires Empty JSON Body

The REST publish/unpublish/schedule endpoints require `Content-Type: application/json` and an empty body `{}`. A POST with no body returns an empty response and may fail silently.

## DELETE Requests May Be CSRF-Blocked

On Cloudflare-deployed instances, DELETE requests via API token may return `403 "Cross-site DELETE form submissions are forbidden"`. Workarounds:
- Use the emdash admin UI for delete operations
- Ensure requests include an `Origin` header matching the emdash instance domain
- Use MCP tools (`content_delete`, `content_permanent_delete`) which handle auth differently

## Translation Gotchas

- Non-translatable fields (defined in schema) sync across all translations in a group automatically
- You cannot create a translation of a translation — always reference the original item
- Each translation gets its own slug, revisions, and publishing status
