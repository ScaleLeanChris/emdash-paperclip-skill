# SEO Gotchas

## SEO Must Be Enabled Per-Collection

By default, collections do **not** have SEO enabled. Attempting to set the `seo` field on content will return:

> `VALIDATION_ERROR: Collection "posts" does not have SEO enabled`

Enable SEO on a collection first:

```bash
PUT /_emdash/api/schema/collections/posts
Body: { "hasSeo": true }
```

This is an ADMIN-only operation. Check `hasSeo` in the collection schema before attempting to set SEO metadata.

## No Dedicated SEO MCP Tools

SEO metadata is managed through content create/update calls (the `seo` field). Redirects and 404 monitoring are REST-only. There are no standalone SEO MCP tools.

## Meta Title vs Content Title

These are different fields:
- `data.title` — The visible post title (H1 on the page)
- `seo.metaTitle` — The `<title>` tag for search results

They can (and often should) differ. The meta title is typically shorter and more keyword-focused.

## Slug Conflicts

If you try to create content with a slug that already exists, emdash appends a suffix (e.g., `my-post-1`). Check the response to confirm the actual slug used.

## Redirect Loops

When creating redirects, verify you're not creating a chain or loop:
- `/a` → `/b` → `/a` (loop — breaks!)
- `/a` → `/b` → `/c` (chain — degrades SEO)

Always redirect directly to the final destination.

## Sitemap is Automatic

You don't need to manually manage the sitemap. EmDash generates it from published content. However, if your sitemap seems stale, check that:
1. Content is actually `published` (not just `draft`)
2. The search index is up to date (`POST /search/rebuild`)

## Meta Description Length

Search engines truncate meta descriptions around 155 characters. Write descriptions that:
- Are 120-155 characters
- Make sense if truncated
- Include a compelling reason to click
- Don't duplicate the title
