---
name: analytics-report
description: Retrieve and summarize PostPeer social analytics for posts, platforms, connected accounts, date ranges, impressions, views, likes, comments, shares, saves, clicks, and engagement. Use when the user asks for performance reports, analytics dashboards, best/worst posts, account metrics, direct platform analytics, or metrics for posts not created through PostPeer.
---

# Analytics Report

## Overview

Use PostPeer analytics to produce concise, caveated performance reports. Use `postpeer-api` for exact tool, REST, or SDK parameters.

## Routing

- Single PostPeer post: call `get_analytics` with `postId`.
- Recent PostPeer posts: call `get_analytics` with `fromDate`, `toDate`, sorting, pagination, and optional platform.
- Direct platform account analytics: call `get_analytics` with `source: "platform"`, `platform`, and `accountId`.
- External platform post ID: include `platform` and `accountId` so PostPeer can resolve it.

## Workflow

1. Resolve date range to absolute `YYYY-MM-DD` values.
2. Resolve account IDs with `list_integrations` if needed.
3. Fetch analytics with the narrowest query that satisfies the request.
4. Sort by the requested metric when provided: date, likes, comments, impressions, views, shares, saves, clicks, or engagement.
5. Report the key result, caveats, and recommended next action.

## Curl Examples

Single PostPeer post:

```bash
curl -s "https://api.postpeer.dev/v1/analytics?postId=post_id" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

Recent posts by date range:

```bash
curl -s "https://api.postpeer.dev/v1/analytics?platform=linkedin&fromDate=2026-04-01&toDate=2026-04-30&sortBy=engagement&order=desc&limit=10" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

Direct platform account analytics:

```bash
curl -s "https://api.postpeer.dev/v1/analytics?source=platform&platform=linkedin&accountId=integration_id&limit=10" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

## Caveats

- Platform APIs differ. Do not compare views and impressions as if they are identical.
- Some platforms expose delayed or partial metrics.
- If a published platform post cannot be matched, report that resolution is needed instead of fabricating metrics.
