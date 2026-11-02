---
name: connect-social-accounts
description: Connect, inspect, group, or disconnect social accounts in PostPeer. Use when the user asks to add OAuth integrations, connect LinkedIn, TikTok, Instagram, YouTube, Facebook, X, Pinterest, Threads, Bluesky, or Google Business, create customer profiles, list connected accounts, generate OAuth URLs, use Bluesky app passwords, or remove integrations.
license: MIT
compatibility: Live operations require the bundled PostPeer MCP connector and outbound HTTPS. The PostPeer access key is collected securely when the plugin is configured. Account connections also require authorization from the social-platform account owner.
---

# Connect Social Accounts

Use the bundled PostPeer MCP tools for every live operation. Never retrieve credentials from the user's machine or ask the user to paste the PostPeer access key into chat. If MCP authentication fails, direct the user to update the key in Claude's plugin configuration UI.

## Workflow

1. If this is for an end user or customer, create or select a profile first with the profile tools.
2. Check supported platforms with `list_platforms` when availability is uncertain.
3. For OAuth platforms, call `get_oauth_url` with optional `profileId`, `redirectUri`, and app selection supported by the tool.
4. Return the OAuth URL for the account owner to open. The agent cannot complete third-party authorization for them.
5. For Bluesky, ask for a Bluesky app password, never the main password, and send it only through `connect_bluesky`. Do not repeat or store it.
6. After authorization, call `list_integrations` with the relevant profile or platform filter.
7. Return integration IDs because posting and analytics use them as `accountId`.

## Profiles

- `create_profile`: create a customer or workspace grouping.
- `list_profiles` and `get_profile`: find an existing group.
- `update_profile`: change the display name, external ID, or metadata.
- `delete_profile`: remove only the grouping; confirm before doing so. Profile deletion does not necessarily delete underlying integrations.

## OAuth Apps

Use BYOK app tools only when the user explicitly wants their own OAuth app credentials: `create_app`, `list_apps`, `get_app`, `update_app`, and `delete_app`. Pass the selected app ID to `get_oauth_url` when supported. Do not use BYOK app tools for Bluesky.

## Safety

- Confirm before disconnecting an integration, deleting a profile or OAuth app, or moving an integration unless the user's explicit request already identifies the target and action.
- Never ask for a social account's main password.
- Treat app secrets and Bluesky app passwords as sensitive; do not echo, log, or save them.
- Do not claim an account is connected until `list_integrations` confirms it.
