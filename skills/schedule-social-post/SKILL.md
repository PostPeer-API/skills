---
name: schedule-social-post
description: Publish now or schedule social posts through PostPeer across TikTok, Instagram, YouTube, LinkedIn, Facebook, X, Pinterest, Bluesky, Threads, Google Business, and other connected platforms. Use when the user asks to create, publish, schedule, queue, cancel, reschedule, draft, or inspect a social post, including multi-platform posts and posts with media.
license: MIT
compatibility: Live operations require the bundled PostPeer MCP connector and outbound HTTPS. The PostPeer access key is collected securely when the plugin is configured. Publishing also requires an authorized target social account.
---

# Schedule Social Post

Use the bundled PostPeer MCP tools for every live operation. Never retrieve credentials from the user's machine or ask the user to paste the PostPeer access key into chat. If MCP authentication fails, direct the user to update the key in Claude's plugin configuration UI.

## Workflow

1. Resolve target accounts with `list_integrations` unless account IDs are supplied.
2. If the post has local media, call `create_media_upload`, transfer only the user-selected file to the returned upload URL, then use the returned public URL in `mediaItems`. Skip upload when the asset already has a public URL.
3. For TikTok, call `get_tiktok_creator_info` and use a returned privacy level while respecting disabled comments, duet, and stitch capabilities.
4. For Pinterest, call `get_pinterest_boards` and include the selected `boardId` in `platformSpecificData`.
5. For Google Business, each integration represents one Business Profile location. Use only platform-specific fields accepted by the current `create_post` tool schema.
6. Build `create_post` with top-level content, optional media items, and one platform object per account.
7. Set one delivery intent: `saveAsDraft: true`, `publishNow: true`, or `scheduledFor` plus `timezone`.
8. Before a write, verify the final content, account IDs, media, and delivery intent. An explicit user request containing those details counts as confirmation.
9. Return the PostPeer post ID, state or scheduled time, target platforms, and any follow-up constraints.

## Payload Guide

Use the connected tool's current schema. A typical scheduled payload contains content, optional public media URLs, platform/account pairs, an absolute scheduled time, and an IANA timezone. Do not invent platform-specific fields or submit raw local paths as media URLs.

## Manage Posts

- Inspect: `list_posts`, `get_post`, or `list_scheduled_posts`.
- Move a queued post: `reschedule_post` with a fresh absolute timestamp and timezone.
- Cancel before delivery: `cancel_scheduled_post`.
- Edit only through an edit tool exposed by the connected server and follow its current schema.
- Delete with `delete_post`; confirm the exact post and whether platform deletion is intended.

## Safety and Pitfalls

- Confirm immediate publishing, scheduling, rescheduling, cancellation, deletion, retry, and any platform-visible edit unless the explicit request already supplies the target and intended change.
- Resolve relative dates to an absolute date, time, and timezone before creating or moving a schedule.
- Do not combine draft, immediate-publish, and scheduled delivery intents.
- Do not omit TikTok privacy level or Pinterest board ID when those platforms require them.
- Upload only media the user selected for this post and do not disclose temporary upload URLs beyond what the workflow needs.
