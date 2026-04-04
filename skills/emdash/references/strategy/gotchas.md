# Strategy Gotchas

## Don't Over-Schedule

Scheduling 20 posts at once creates a backlog that's hard to manage. Better to:
- Plan a week at a time
- Create and schedule in batches of 3-5
- Leave room for reactive/timely content

## Content Calendar != Content Quality

Having a cadence means nothing if the content is thin. One great post per week beats five mediocre ones. Adjust cadence to match your quality capacity.

## Audit Before Planning

Always audit existing content before planning new content. You may find:
- Existing posts that just need updating (cheaper than writing new)
- Duplicate topics that should be consolidated
- Underperforming posts that need promotion, not more content

## Topic Cannibalization

Multiple posts targeting the same keyword compete with each other in search. Before creating a new post, search existing content:

```
search({ query: "target keyword" })
```

If a similar post exists, update it instead of creating a new one.

## Scheduling Timezone

All `scheduledAt` times are in **UTC**. Convert from your target audience's timezone:
- 9:00 AM EST = 14:00 UTC
- 9:00 AM PST = 17:00 UTC
- 9:00 AM CET = 08:00 UTC

## Draft Expiry

Drafts don't expire in emdash, but stale drafts create clutter. Periodically review drafts:

```
content_list({ collection: "posts", status: "draft" })
```

Delete or publish drafts that have been sitting for more than 30 days.
