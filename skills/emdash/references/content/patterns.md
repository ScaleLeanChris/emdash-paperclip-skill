# Content Patterns

## Standard Blog Post Workflow

### 1. Create Draft

```
content_create({
  collection: "posts",
  data: { title: "...", body: [...portableTextBlocks...] },
  slug: "my-post",
  status: "draft"
})
```

### 2. Add SEO Metadata

Include `seo` in the create or update call:

```json
{
  "seo": {
    "metaTitle": "Title | Site Name",
    "metaDescription": "155 chars max description..."
  }
}
```

### 3. Assign Taxonomy Terms

Use **singular** taxonomy names (`category`, `tag`):

```
PUT /content/posts/{id}/terms/category
Body: { "termIds": ["cat-id-1"] }

PUT /content/posts/{id}/terms/tag
Body: { "termIds": ["tag-id-1", "tag-id-2"] }
```

**Note:** This endpoint may not be available on all deployments (returned 404 in testing). Use the emdash admin UI as fallback.

### 4. Review Draft

Use `content_compare` to diff live vs draft before publishing.

### 5. Publish or Schedule

- Immediate: `content_publish({ collection: "posts", id: "..." })`
- Future: `content_schedule({ collection: "posts", id: "...", scheduledAt: "2026-04-15T09:00:00Z" })`

## Bulk Content Creation

Create multiple posts in sequence. Each `content_create` is independent:

```
for each post in batch:
  1. content_create(post)
  2. Assign taxonomy terms via REST
  3. content_publish or content_schedule
```

There is no bulk create endpoint — use sequential calls.

## Revision Management

### Check revision history
```
revision_list({ collection: "posts", id: "post-id" })
```

### Restore a previous version
```
revision_restore({ revisionId: "rev-id" })
```

This creates a new revision with the restored content and makes it the current draft.

## Content Translation

### Create a translation
```
content_create({
  collection: "posts",
  data: { title: "Mon Article", body: [...] },
  locale: "fr",
  translationOf: "original-post-id"
})
```

### List all translations
```
content_translations({ collection: "posts", id: "original-post-id" })
```

## Scheduled Publishing Cadence

For a regular publishing schedule (e.g., 3x/week):

1. Create all drafts with content
2. Schedule each with staggered `scheduledAt` times:
   - Monday 9:00 UTC
   - Wednesday 9:00 UTC
   - Friday 9:00 UTC
3. Posts auto-publish at the scheduled times

## Content Duplication

To create a post based on an existing template:

```
content_duplicate({ collection: "posts", id: "template-post-id" })
```

Then update the duplicate with new content.

## Comment Moderation

Poll the moderation inbox and act on pending comments:

```
1. GET /admin/comments?status=pending
2. Review each comment
3. PUT /admin/comments/{id}/status  Body: { "status": "approved" }
   or bulk: POST /admin/comments/bulk  Body: { "ids": [...], "action": "approve" }
```
