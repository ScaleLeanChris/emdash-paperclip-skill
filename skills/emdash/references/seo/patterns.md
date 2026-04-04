# SEO Patterns

## On-Page SEO Checklist

For every post, ensure:

1. **Meta title** — Under 60 chars, includes primary keyword, ends with `| Site Name`
2. **Meta description** — 120-155 chars, includes keyword, has a call to action
3. **Slug** — Short, keyword-rich, no stop words (e.g., `/seo-guide` not `/the-complete-guide-to-seo`)
4. **H1** — Matches the post title, includes primary keyword
5. **Image alt text** — Descriptive, includes keyword where natural

```json
{
  "data": {
    "title": "How to Build a Blog with EmDash",
    "body": [...]
  },
  "slug": "build-blog-emdash",
  "seo": {
    "metaTitle": "How to Build a Blog with EmDash | Your Site",
    "metaDescription": "Learn how to set up and deploy a modern blog using EmDash CMS on Cloudflare. Step-by-step guide with examples."
  }
}
```

## Slug Change with Redirect

When renaming a URL:

```
1. Note the current slug: "old-post-name"
2. Update content with new slug:
   content_update({ slug: "new-post-name", _rev: "..." })
3. Create a 301 redirect:
   POST /redirects
   Body: { "source": "/blog/old-post-name", "destination": "/blog/new-post-name", "type": 301 }
```

## 404 Monitoring and Fixes

Regular maintenance workflow:

```
1. GET /redirects/404s/summary?limit=20
2. For each high-traffic 404:
   a. Identify the intended destination
   b. POST /redirects with source → destination mapping
3. Re-check after a week
```

## Internal Linking

When creating content, link to related posts using Portable Text links:

```json
{
  "_type": "block",
  "children": [
    { "_type": "span", "text": "See our " },
    { "_type": "span", "text": "SEO guide", "marks": ["link-1"] },
    { "_type": "span", "text": " for more details." }
  ],
  "markDefs": [
    { "_key": "link-1", "_type": "link", "href": "/blog/seo-guide" }
  ]
}
```

Use relative URLs for internal links to avoid issues with domain changes.

## Content Freshness

Search engines favor fresh content. Regularly:

1. List published posts sorted by `updated_at` (oldest first)
2. Identify posts that haven't been updated in 6+ months
3. Review and update with current information
4. Re-publish to update the `updated_at` timestamp
