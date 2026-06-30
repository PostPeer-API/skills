---
name: schedule-social-post
description: Publish now or schedule social posts through PostPeer across TikTok, Instagram, YouTube, LinkedIn, Facebook, X/Twitter, Pinterest, Bluesky, Threads, and other connected platforms. Use when the user asks to create, publish, schedule, queue, cancel, reschedule, draft, or inspect a social post, including multi-platform posts and posts with media.
---

# Schedule Social Post

## Overview

Create valid PostPeer posting workflows. Use `postpeer-api` for tool names, auth, REST examples, and platform prerequisites.

## Workflow

1. Resolve target accounts with `list_integrations` unless account IDs are supplied.
2. If the post has local media, call `create_media_upload`, upload the file bytes to the returned `uploadUrl`, then use the returned `publicUrl` in `mediaItems`.
3. For TikTok, call `get_tiktok_creator_info` and choose a returned privacy level.
4. For Pinterest, call `get_pinterest_boards` and include `platformSpecificData.boardId`.
5. Build `create_post` with top-level `content`, optional `mediaItems`, and one platform object per account.
6. Use `publishNow: true` for immediate publish. Use `scheduledFor` and `timezone` for future delivery.
7. Return the PostPeer post ID, scheduled time, target platforms, and any follow-up constraints.

## Payload Shape

```json
{
  "content": "Post text",
  "mediaItems": [
    { "type": "image", "url": "https://..." }
  ],
  "platforms": [
    {
      "platform": "linkedin",
      "accountId": "integration_id",
      "content": "Optional platform-specific override"
    }
  ],
  "scheduledFor": "2026-07-01T09:00:00",
  "timezone": "America/New_York"
}
```

## Curl Examples

Create an immediate post:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/posts" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Hello from PostPeer",
    "platforms": [
      {
        "platform": "linkedin",
        "accountId": "integration_id"
      }
    ],
    "publishNow": true
  }'
```

Schedule a post:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/posts" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Scheduled post text",
    "platforms": [
      {
        "platform": "linkedin",
        "accountId": "integration_id"
      }
    ],
    "scheduledFor": "2026-07-01T09:00:00",
    "timezone": "America/New_York"
  }'
```

Upload local media first:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/media/upload" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "filename": "video.mp4",
    "mimeType": "video/mp4"
  }'
```

Then PUT the file bytes to the returned `uploadUrl`, and use the returned `publicUrl` in `mediaItems`.

## Manage Scheduled Posts

- List queue: `list_scheduled_posts`.
- Move time: `reschedule_post` with a fresh ISO timestamp and timezone.
- Cancel before delivery: `cancel_scheduled_post`.
- Delete published records: `delete_post`; only use platform deletion when the user explicitly requests it.

REST equivalents:

```bash
curl -s "https://api.postpeer.dev/v1/posts/scheduled?limit=10" \
  -H "x-access-key: $POSTPEER_API_KEY"

curl -s -X PATCH "https://api.postpeer.dev/v1/posts/scheduled/post_id" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "scheduledFor": "2026-07-01T12:00:00", "timezone": "America/New_York" }'

curl -s -X DELETE "https://api.postpeer.dev/v1/posts/scheduled/post_id" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

## Common Pitfalls

- Do not schedule relative dates without resolving them to an absolute date and timezone.
- Do not pass raw local media paths as `mediaItems.url`.
- Do not omit TikTok privacy level when TikTok is targeted.
- Do not omit Pinterest board ID when Pinterest is targeted.
