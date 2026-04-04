# Distribution Gotchas

## Construct URLs Carefully

Post URLs follow the pattern: `$EMDASH_URL/{collection-path}/{slug}`

The exact path depends on the Astro template. Common patterns:
- `/blog/{slug}` for blog posts
- `/{slug}` for pages
- `/portfolio/{slug}` for portfolio items

Check the site's routing configuration if unsure.

## Hermes Channel IDs

Hermes channels are identified by conversation IDs, not names. Use `mcp__hermes__channels_list()` to discover available channels and their IDs before sending.

## Don't Spam Channels

Distribute thoughtfully:
- Don't send the same message to every channel
- Adapt content for each platform's audience
- Respect channel frequency expectations (daily digest vs. instant notification)

## RSS is Automatic

EmDash auto-generates RSS feeds from published content. Don't manually create RSS entries. The feed URL is typically `$EMDASH_URL/rss.xml` or `$EMDASH_URL/feed.xml`.

## Newsletter Tools Are External

EmDash doesn't have a built-in newsletter system. The agent's role is to:
1. Fetch recent published content from emdash
2. Draft newsletter copy
3. Prepare it for the external newsletter tool (Mailchimp, ConvertKit, Buttondown, etc.)

The actual sending is outside emdash's scope.

## Social Media Character Limits

| Platform | Limit |
|----------|-------|
| Twitter/X | 280 characters |
| LinkedIn | 3,000 characters |
| Facebook | 63,206 characters |
| Instagram caption | 2,200 characters |
| Discord message | 2,000 characters |
| Slack message | 40,000 characters |

Always check post length before sending.

## Timing Matters

Best posting times vary by platform and audience. General guidelines:
- Twitter/X: Weekdays 8-10am, 12-1pm
- LinkedIn: Tuesday-Thursday 8-10am
- Discord/Slack: When the community is active (check history)

Stagger distribution over multiple days for maximum reach.
