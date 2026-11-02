---
name: webhook-notifications
description: Configure PostPeer lifecycle notifications for scheduled, published, partial, and failed posts through webhook endpoints or email recipients. Use when the user asks for webhooks, callbacks, alerts, publish-status events, failure monitoring, email alerts, testing delivery, or updating notification subscriptions.
license: MIT
compatibility: Live operations require the bundled PostPeer MCP connector and outbound HTTPS. The PostPeer access key is collected securely when the plugin is configured. Webhooks require a reachable HTTPS endpoint; email delivery requires authorized recipients.
---

# Webhook Notifications

Use the bundled PostPeer MCP tools for every live operation. Never retrieve credentials from the user's machine or ask the user to paste the PostPeer access key into chat. If MCP authentication fails, direct the user to update the key in Claude's plugin configuration UI.

Prefer notifications over repeated polling when the user wants ongoing delivery status.

## Workflow

1. Select event types supported by the connected tool, commonly scheduled, published, partial, and failed post lifecycle events.
2. Select a webhook or email channel.
3. Select the scope: all profiles, one `profileId`, or scheduled posts only, when supported.
4. For a webhook, validate that the destination uses HTTPS. Use `test_notification` only after confirming the destination because a test sends external data.
5. Before creating or changing a subscription, verify the destination, event types, enabled state, and scope. An explicit request containing those details counts as confirmation.
6. Create or update with `create_notification` or `update_notification` using the current MCP schema.
7. Return the notification ID, events, channel, enabled state, and scope without exposing secrets.

## Manage Subscriptions

- List with `list_notifications`.
- Inspect with `get_notification`.
- Update with `update_notification`.
- Delete with `delete_notification` only after confirming the exact subscription unless explicitly requested.

If the channel is immutable in the current schema, explain that switching between webhook and email requires deleting and recreating the subscription, and confirm both actions before proceeding.

## Safety

- Webhook URLs must use HTTPS.
- Treat webhook URLs, signing secrets, and email recipients as sensitive operational data; disclose them only as needed to confirm the requested destination.
- Confirm test sends, subscription creation, destination or recipient changes, enable/disable changes, and deletion unless the user's explicit request already identifies the exact change.
- Never include access keys or social credentials in webhook payloads or responses.
