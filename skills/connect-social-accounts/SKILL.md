---
name: connect-social-accounts
description: Connect, inspect, group, or disconnect social accounts in PostPeer. Use when the user asks to add OAuth integrations, connect LinkedIn/TikTok/Instagram/YouTube/Facebook/X/Pinterest/Threads/Bluesky, create customer profiles, list connected accounts, generate OAuth URLs, use Bluesky app passwords, or remove integrations.
---

# Connect Social Accounts

## Overview

Guide agents through connecting social accounts to PostPeer. Use `postpeer-api` for tool names, REST examples, and auth.

## Workflow

1. If this is for an end user or customer, create or select a profile first.
2. Check supported platforms with `list_platforms` when platform availability is uncertain.
3. For OAuth platforms, call `get_oauth_url` with optional `profileId` and `redirectUri`.
4. For Bluesky, ask for handle plus app password and call `connect_bluesky`.
5. After the user completes auth, call `list_integrations` with the profile filter when relevant.
6. Return integration IDs because posting and analytics use them as `accountId`.

## Curl Examples

Create an OAuth URL:

```bash
curl -s "https://api.postpeer.dev/v1/connect/linkedin?profileId=profile_id&redirectUri=https%3A%2F%2Fexample.com%2Fconnected" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

List connected integrations:

```bash
curl -s "https://api.postpeer.dev/v1/connect/integrations?platform=linkedin&limit=10" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

Create a profile:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/profiles" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{ "name": "Acme Co" }'
```

Connect Bluesky with an app password:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/connect/bluesky/auth" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "identifier": "alice.bsky.social",
    "password": "bluesky-app-password"
  }'
```

Disconnect an integration:

```bash
curl -s -X DELETE "https://api.postpeer.dev/v1/connect/integrations/integration_id" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

## Profiles

Use profiles as logical customer or workspace containers. They do not delete underlying integrations when deleted.

Use:

- `create_profile` for a new customer/end user.
- `list_profiles` to find existing groups.
- `update_profile` for display name, external ID, or metadata.
- `delete_profile` only when the user wants to remove the grouping.

## OAuth Apps

Only use BYOK app tools when the user explicitly wants their own OAuth app credentials:

- `create_app`, `list_apps`, `get_app`, `update_app`, `delete_app`.
- Pass the app ID to `get_oauth_url` when connecting through customer-owned OAuth keys.
- Do not use BYOK for Bluesky; Bluesky uses app passwords.

## Safety

- Never ask for a user's main Bluesky password; ask for an app password.
- Do not disconnect integrations unless explicitly requested.
- Include OAuth URLs as user action items; the agent cannot complete third-party authorization by itself.
