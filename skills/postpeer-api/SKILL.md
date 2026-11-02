---
name: postpeer-api
description: Route PostPeer MCP and developer-reference work. Use when the user asks about PostPeer tools, postpeer.dev, MCP, REST or OpenAPI schemas, TypeScript SDK usage, platform prerequisites, authentication architecture, or low-level tool routing. For live publishing, connections, analytics, and notifications, use the bundled PostPeer MCP tools and prefer the focused PostPeer skills.
license: MIT
compatibility: Live operations require the bundled PostPeer MCP connector and outbound HTTPS. The PostPeer access key is collected securely when the plugin is configured.
---

# PostPeer API

## Execution Boundary

Use the bundled PostPeer MCP server for every live PostPeer operation. Its access key is collected in the plugin configuration UI and passed securely to the declared server.

- Do not look for credentials in shell configuration, local files, or process state.
- Do not ask the user to paste an access key into chat.
- Do not make authenticated REST requests or launch a local MCP package.
- If the MCP server is unavailable or authentication fails, explain how to update the plugin's PostPeer API key in Claude's plugin configuration UI, then stop the live operation.

REST, SDK, and OpenAPI information may be discussed when the user is building their own integration. Treat it as developer reference, not as this plugin's execution path. The OpenAPI source of truth is `https://api.postpeer.dev/documentation/json`.

## MCP Tool Routing

The names below are exposed by PostPeer's hosted MCP documentation and implementation:

- Posts: `create_post`, `list_posts`, `get_post`, `delete_post`.
- Scheduling: `list_scheduled_posts`, `cancel_scheduled_post`, `reschedule_post`.
- Connections: `get_oauth_url`, `connect_bluesky`, `list_integrations`, `disconnect_integration`.
- Profiles: `create_profile`, `list_profiles`, `get_profile`, `update_profile`, `delete_profile`.
- Analytics: `get_analytics` and the platform-specific analytics tools returned by the server.
- Platforms: `list_platforms`, `get_pinterest_boards`, `get_tiktok_creator_info`, and other platform helpers returned by the server.
- Media: `create_media_upload`, plus media-management tools returned by the server.
- AI helpers: `ai_write_content`, `ai_generate_image`.
- BYOK apps: `create_app`, `list_apps`, `get_app`, `update_app`, `delete_app`.
- Notifications: `test_notification`, `create_notification`, `list_notifications`, `get_notification`, `update_notification`, `delete_notification`.

When a capability is not listed here, discover it from the connected PostPeer server instead of inventing a tool name or request shape.

## Hard Rules

- Set one delivery intent: `saveAsDraft: true`, `publishNow: true`, or `scheduledFor` plus `timezone`.
- Use `create_media_upload` only for local media that needs a public URL. Skip it when the asset already has a public URL.
- TikTok requires `get_tiktok_creator_info` before publishing. Use a returned `privacyLevelOptions` value and respect disabled comment, duet, and stitch flags.
- Pinterest requires `get_pinterest_boards`; pass the selected `boardId` in `platformSpecificData`.
- Use profiles to group integrations by end user, customer, or workspace.
- Prefer notifications over repeated polling for publish lifecycle events.
- Do not invent platform-specific fields. Discover them from PostPeer helpers or current documentation.
- Cancel queued posts with `cancel_scheduled_post`; delete post records with `delete_post`.
- Confirm publishing, deleting, disconnecting, webhook creation or tests, recipient changes, and other external changes unless the user's explicit request already provides the target and intended change.

## Output Standards

- State which PostPeer MCP tools were used.
- Include IDs needed for follow-up operations.
- Call out platform prerequisites and user action required for OAuth.
- Never expose access keys, social credentials, webhook secrets, or other sensitive values.
