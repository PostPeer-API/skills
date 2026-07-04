---
name: postpeer-api
description: Route raw PostPeer MCP, REST API, curl, OpenAPI, and TypeScript SDK work. Use when the user asks for PostPeer API docs, postpeer.dev, postpeer-mcp, @postpeer/node, curl examples, REST API examples, OpenAPI paths, endpoint schemas, auth, SDK usage, platform prerequisite lookup, or low-level tool routing. For concrete workflows, prefer the focused PostPeer skills for scheduling, account connections, analytics, and notifications.
---

# PostPeer API

## Overview

Use this as the shared PostPeer routing layer. The default path is the REST API with `curl`, which needs nothing beyond a `POSTPEER_API_KEY`. MCP is optional: if the client already has the PostPeer MCP tools configured, use them; if not, REST covers every capability on its own. The TypeScript SDK (`@postpeer/node`) is also available for code integrations.

Do not build or suggest a separate CLI unless the user explicitly asks for terminal-first batch workflows.

## Access Paths

- REST API (default): `https://api.postpeer.dev` with `x-access-key: <access key>`. Needs only a `POSTPEER_API_KEY`.
- OpenAPI spec: `https://api.postpeer.dev/documentation/json`.
- SDK: install `@postpeer/node`, then instantiate with `POSTPEER_API_KEY`.
- Hosted MCP (optional): `https://mcp.postpeer.dev/mcp` with `Authorization: Bearer <access key>`.
- npm MCP (optional): `POSTPEER_API_KEY=<key> npx -y postpeer-mcp@latest`.
- Dashboard access keys: `https://postpeer.dev/dashboard`.

Do not ask the user for an API key unless a live call is required and no key is configured.

## REST API with curl

REST is the default execution path and needs only a `POSTPEER_API_KEY`. Use it directly; reach for MCP tools only when the client already has them configured.

Authentication. The skills read the key from `POSTPEER_API_KEY` (for example a `.env` file with `POSTPEER_API_KEY=...`). Never print the key back or commit `.env`:

```bash
export POSTPEER_API_KEY="YOUR_POSTPEER_ACCESS_KEY"
```

Health check:

```bash
curl -s "https://api.postpeer.dev/v1/health" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

List connected integrations:

```bash
curl -s "https://api.postpeer.dev/v1/connect/integrations" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

Create a scheduled post:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/posts" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "content": "Post text",
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

Use `https://api.postpeer.dev/documentation/json` as the REST source of truth. If an endpoint path, request body, enum, or response shape is uncertain, fetch the OpenAPI spec or use MCP docs/search before inventing a value.

## Tool Routing

- Posting: `create_post`, `list_posts`, `get_post`, `delete_post`.
- Scheduling: `list_scheduled_posts`, `cancel_scheduled_post`, `reschedule_post`.
- Connections: `get_oauth_url`, `connect_bluesky`, `list_integrations`, `disconnect_integration`.
- Profiles: `create_profile`, `list_profiles`, `get_profile`, `update_profile`, `delete_profile`.
- Analytics: `get_analytics`.
- Platforms: `list_platforms`, `get_pinterest_boards`, `get_tiktok_creator_info`.
- Media: `create_media_upload`.
- AI helpers: `ai_write_content`, `ai_generate_image`.
- BYOK apps: `create_app`, `list_apps`, `get_app`, `update_app`, `delete_app`.
- Notifications: `test_notification`, `create_notification`, `list_notifications`, `get_notification`, `update_notification`, `delete_notification`.

## Hard Rules

- Set either `publishNow: true` or `scheduledFor` plus `timezone`, not both as competing intents.
- Use `create_media_upload` only for local media that needs a public URL. Skip it when the asset already has a public URL.
- TikTok requires `get_tiktok_creator_info` before publishing. Use one returned `privacyLevelOptions` value and respect disabled comment, duet, and stitch flags.
- Pinterest requires `get_pinterest_boards`; pass the selected `boardId` in `platformSpecificData`.
- Use profiles to group integrations by end user, customer, or workspace.
- Use notifications for publish lifecycle events instead of polling by default.
- Do not invent platform-specific fields. Discover them from PostPeer helpers or existing docs.
- Scheduled posts should be canceled with `cancel_scheduled_post`; published records use `delete_post`.

## Common REST Endpoints

- `GET /v1/health`
- `GET /v1/health/auth`
- `GET /v1/connect/{platform}`
- `POST /v1/connect/bluesky/auth`
- `GET /v1/connect/integrations`
- `DELETE /v1/connect/integrations/{id}`
- `POST /v1/posts`
- `GET /v1/posts`
- `GET /v1/posts/{postId}`
- `DELETE /v1/posts/{postId}`
- `GET /v1/posts/scheduled`
- `DELETE /v1/posts/scheduled/{postId}`
- `PATCH /v1/posts/scheduled/{postId}`
- `POST /v1/media/upload`
- `GET /v1/analytics`
- `GET /v1/pinterest/boards`
- `GET /v1/tiktok/creator-info`
- `POST /v1/notifications/test`
- `POST /v1/notifications`
- `GET /v1/notifications`
- `GET /v1/notifications/{id}`
- `PATCH /v1/notifications/{id}`
- `DELETE /v1/notifications/{id}`

## Output Standards

- State which tools or API surfaces were used.
- Include IDs needed for follow-up operations.
- Call out platform prerequisites and user action required for OAuth.
- Do not expose access keys or secrets.
