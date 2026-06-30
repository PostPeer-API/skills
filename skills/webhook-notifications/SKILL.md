---
name: webhook-notifications
description: Configure PostPeer lifecycle notifications for post.scheduled, post.published, post.partial, post.failed, webhook endpoints, and email recipients. Use when the user asks for webhooks, callbacks, alerts, notifications, publish status events, failure monitoring, email alerts, testing webhook delivery, or updating notification subscriptions.
---

# Webhook Notifications

## Overview

Use PostPeer notifications for publish lifecycle events. Prefer notifications over repeated polling when the user wants ongoing delivery status.

## Workflow

1. Decide event types: `post.scheduled`, `post.published`, `post.partial`, and `post.failed`.
2. Decide delivery channel: webhook or email.
3. Decide scope: all profiles, one `profileId`, or scheduled posts only.
4. For webhooks, test with `test_notification` before saving when possible.
5. Create or update the subscription with `create_notification` or `update_notification`.
6. Return the notification ID, events, channel, and scope.

## Manage Subscriptions

- List: `list_notifications`.
- Inspect: `get_notification`.
- Update channel settings: `update_notification`.
- Delete: `delete_notification`.

Channel is immutable. To switch from webhook to email, delete and recreate.

## Curl Examples

Test a webhook destination:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/notifications/test" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "webhook",
    "webhook": {
      "url": "https://example.com/postpeer/webhook"
    }
  }'
```

Create a webhook subscription:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/notifications" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "webhook",
    "eventTypes": ["post.published", "post.failed"],
    "webhook": {
      "url": "https://example.com/postpeer/webhook"
    },
    "enabled": true
  }'
```

Create an email notification:

```bash
curl -s -X POST "https://api.postpeer.dev/v1/notifications" \
  -H "x-access-key: $POSTPEER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "channel": "email",
    "eventTypes": ["post.failed"],
    "email": {
      "recipients": ["ops@example.com"]
    },
    "enabled": true
  }'
```

List subscriptions:

```bash
curl -s "https://api.postpeer.dev/v1/notifications" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

## Safety

- Webhook URLs must be HTTPS.
- Do not expose webhook signing secrets or credentials.
- Scope by `profileId` when the notification belongs to one customer or workspace.
