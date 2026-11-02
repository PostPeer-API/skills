---
name: analytics-report
description: Retrieve and summarize PostPeer social analytics for posts, platforms, connected accounts, date ranges, impressions, views, likes, comments, shares, saves, clicks, and engagement. Use when the user asks for performance reports, analytics dashboards, best or worst posts, account metrics, direct platform analytics, or metrics for posts not created through PostPeer.
license: MIT
compatibility: Live analytics require the bundled PostPeer MCP connector and outbound HTTPS. The PostPeer access key is collected securely when the plugin is configured. Results depend on the metrics each platform exposes.
---

# Analytics Report

Use the bundled PostPeer MCP tools for live analytics. Never retrieve credentials from the user's machine or ask the user to paste a secret into chat. If authentication fails, direct the user to update the PostPeer API key in Claude's plugin configuration UI.

## Routing

- Single PostPeer post: call `get_analytics` with `postId`.
- Recent PostPeer posts: call `get_analytics` with `fromDate`, `toDate`, sorting, pagination, and an optional platform.
- Direct platform account analytics: call `get_analytics` with `source: "platform"`, `platform`, and `accountId`.
- External platform post ID: include `platform` and `accountId` so PostPeer can resolve it.
- If the connected server advertises a more specific analytics tool for the requested platform, use that documented tool rather than inventing parameters.

## Workflow

1. Resolve the date range to absolute `YYYY-MM-DD` values and state the timezone used.
2. Resolve account IDs with `list_integrations` when needed.
3. Fetch analytics with the narrowest MCP query that satisfies the request.
4. Sort by the requested metric when supported: date, likes, comments, impressions, views, shares, saves, clicks, or engagement.
5. Report the key result, data limitations, and a useful next action.

## Caveats

- Platform APIs differ. Do not compare views and impressions as if they were identical.
- Some platforms expose delayed, sampled, or partial metrics.
- If a published platform post cannot be matched, report that resolution is needed instead of fabricating metrics.
- Reading analytics is non-destructive. Confirm before any follow-up action that changes posts, accounts, or notification settings unless the user explicitly requested it.
