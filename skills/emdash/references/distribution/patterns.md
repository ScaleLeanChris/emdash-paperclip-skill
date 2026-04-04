# Distribution Patterns

## Post-Publish Distribution Workflow

After publishing a post, distribute across channels:

```
1. Fetch the published post:
   content_get({ collection: "posts", id: "post-id" })

2. Extract key info:
   - Title
   - Meta description (from seo field)
   - URL: $EMDASH_URL/blog/{slug}
   - Key points from the body

3. Create platform-specific content:
   - Twitter/X: 280 chars, hook + link
   - LinkedIn: Professional tone, 3-5 paragraphs
   - Discord/Slack: Brief intro + link
   - Email newsletter: Summary + CTA

4. Post to each channel using available tools
```

## Social Media Templates

### Twitter/X (280 chars)

```
{Hook — surprising stat or question}

{Key takeaway in 1-2 sentences}

Read more: {URL}
```

### LinkedIn (Professional)

```
{Opening hook — problem or insight}

{3-5 bullet points with key takeaways}

{Call to action}

{URL}

#relevanthashtag #anotherhashtag
```

### Discord/Slack (Community)

```
**New Post: {Title}**

{1-2 sentence summary}

{URL}
```

## Content Repurposing

Transform one blog post into multiple formats:

| Source | Derivative | How |
|--------|-----------|-----|
| Long-form post | Twitter thread | Extract 5-7 key points, one per tweet |
| Tutorial post | Short video script | Outline the steps as narration |
| Data-driven post | Infographic brief | Pull stats and structure visually |
| Listicle | Carousel post | One slide per list item |
| Case study | Testimonial snippet | Extract customer quotes |

## Newsletter Content

Draft newsletter content from recent posts:

```
1. List recently published posts:
   content_list({ collection: "posts", status: "published", orderBy: "publishedAt", order: "desc", limit: 5 })

2. For each post, write a newsletter blurb:
   - Headline
   - 2-3 sentence summary
   - "Read more →" link

3. Compile into newsletter format with intro and outro
```

## Cross-Channel Distribution Calendar

Stagger distribution to maximize reach:

```
Day 0 (publish day):
  - Publish post on site
  - Share on Twitter/X
  - Share in community channels

Day 1:
  - Share on LinkedIn with professional angle
  - Post in relevant forums

Day 3:
  - Include in weekly newsletter

Day 7:
  - Re-share on Twitter with different angle
  - Share key takeaway as standalone social post
```
