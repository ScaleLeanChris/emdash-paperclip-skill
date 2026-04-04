# Strategy Patterns

## Content Audit

Assess what exists before planning what's next:

```
1. content_list({ collection: "posts", status: "published", limit: 100 })
2. For each post, note:
   - Title and slug
   - Published date (how old?)
   - Taxonomy terms (what topics?)
   - Word count / content depth
3. Identify:
   - Gaps: topics not yet covered
   - Stale: posts older than 6 months without updates
   - Thin: posts that need more depth
   - Duplicates: overlapping topics that should be consolidated
```

## Define Content Pillars

Content pillars are 3-5 core topics. Every post should map to a pillar.

```
Example pillars for a developer tools blog:
1. Getting Started (tutorials, quickstarts)
2. Best Practices (patterns, architecture)
3. Deep Dives (technical internals, advanced)
4. Case Studies (customer stories, benchmarks)
5. Product Updates (releases, changelogs)
```

Map pillars to taxonomy categories for organization.

## Build a Content Calendar

### Weekly cadence (3 posts/week)

```
Week of April 7, 2026:
├── Mon: "Getting Started with EmDash" (Pillar: Getting Started) → schedule 9:00 UTC
├── Wed: "SEO Best Practices for Blogs" (Pillar: Best Practices) → schedule 9:00 UTC
└── Fri: "How Company X Migrated from WordPress" (Pillar: Case Studies) → schedule 9:00 UTC
```

### Implementation in emdash

```
1. Create all three posts as drafts:
   content_create({ collection: "posts", data: {...}, status: "draft" })

2. Schedule each:
   content_schedule({ collection: "posts", id: "post-1", scheduledAt: "2026-04-07T09:00:00Z" })
   content_schedule({ collection: "posts", id: "post-2", scheduledAt: "2026-04-09T09:00:00Z" })
   content_schedule({ collection: "posts", id: "post-3", scheduledAt: "2026-04-11T09:00:00Z" })
```

## Topic Research

When planning topics, consider:

1. **Search demand** — What are people searching for? Use keyword research tools.
2. **Content gaps** — What do competitors cover that you don't?
3. **Customer questions** — What do users ask in support/sales?
4. **Trending topics** — What's new in the space?

Use the emdash search API to check if you already have content on a topic:

```
search({ query: "keyword", collection: "posts" })
```

## Content Refresh Strategy

Systematic approach to keeping content current:

```
1. List all published posts sorted by updatedAt (oldest first)
2. For posts older than 6 months:
   a. Is the information still accurate?
   b. Are there new developments to add?
   c. Can internal links be added to newer posts?
3. Update content and re-publish to refresh timestamps
```

## Pillar + Cluster SEO Strategy

Create topic authority through linked content clusters:

```
Pillar post: "The Complete Guide to Blog SEO" (comprehensive, 2000+ words)
  ├── Cluster: "How to Write Meta Descriptions" → links to pillar
  ├── Cluster: "Slug Best Practices for URLs" → links to pillar
  ├── Cluster: "Internal Linking Strategy" → links to pillar
  └── Cluster: "How to Fix 404 Errors" → links to pillar

The pillar post links back to each cluster post.
```

Map this to emdash taxonomy: pillar = category, clusters = posts in that category.
