# PostPeer Skills

PostPeer Skills is a Claude Code plugin for publishing, scheduling, analyzing, and managing social media posts through [PostPeer](https://postpeer.dev). Installing it adds five focused skills and the hosted PostPeer MCP connector for TikTok, Instagram, YouTube, LinkedIn, Facebook, X, Pinterest, Threads, Bluesky, and Google Business Profile.

## Secure configuration

When the plugin is enabled, Claude prompts for a PostPeer access key in the plugin configuration UI. The field is required and sensitive, so Claude stores it in the platform's secure credential store and supplies it only to the declared PostPeer MCP server. The skills do not read credentials from shell configuration or local files, and users should never paste access keys into chat.

Create or manage an access key in the [PostPeer dashboard](https://postpeer.dev/dashboard). The plugin connects to the hosted [PostPeer MCP endpoint](https://mcp.postpeer.dev/mcp); nothing is installed or executed locally by this plugin.

## Included skills

| Command | Use it to |
| --- | --- |
| [`/postpeer-skills:postpeer-api`](skills/postpeer-api/SKILL.md) | Route PostPeer MCP operations and developer-reference questions |
| [`/postpeer-skills:schedule-social-post`](skills/schedule-social-post/SKILL.md) | Publish, draft, schedule, reschedule, cancel, or inspect posts |
| [`/postpeer-skills:connect-social-accounts`](skills/connect-social-accounts/SKILL.md) | Connect, inspect, group, or disconnect social accounts |
| [`/postpeer-skills:analytics-report`](skills/analytics-report/SKILL.md) | Retrieve and summarize post or account analytics |
| [`/postpeer-skills:webhook-notifications`](skills/webhook-notifications/SKILL.md) | Configure webhook or email lifecycle notifications |

Claude can also invoke these skills automatically when a request matches a skill's description.

## Usage examples

```text
/postpeer-skills:schedule-social-post Schedule this video to TikTok and Instagram tomorrow at 9:00 AM America/New_York time.
```

```text
/postpeer-skills:connect-social-accounts Connect a customer's LinkedIn and YouTube accounts, then list their integrations.
```

```text
/postpeer-skills:webhook-notifications Create failed-publish webhook alerts for profile profile_123.
```

```text
/postpeer-skills:analytics-report Summarize last month's LinkedIn and TikTok performance and identify the best posts.
```

Publishing, deleting posts, disconnecting accounts, and notification changes affect external systems. The skills require confirmation of the target, content, timing, and destination unless the user's explicit request already supplies those details.

## Services and permissions

| Service or permission | Requirement |
| --- | --- |
| PostPeer account and access key | Required for authenticated MCP operations; configured securely when the plugin is enabled |
| Hosted PostPeer MCP | Outbound HTTPS to `mcp.postpeer.dev` is required for live operations |
| PostPeer API | The hosted MCP server communicates with `api.postpeer.dev` on the user's behalf |
| Social-platform authorization | Required for each platform the user connects or publishes to; OAuth completes in the account owner's browser, while Bluesky requires an app password rather than the main password |
| Local media | Used only when the user asks to publish a local asset; PostPeer returns a temporary upload URL |
| Webhook endpoint or email recipient | Required only when the user asks to configure lifecycle notifications; webhook destinations must use HTTPS |

## Developer references

- [PostPeer documentation](https://postpeer.dev/docs)
- [Hosted MCP documentation](https://postpeer.dev/docs/ai-agents/mcp)
- [OpenAPI schema](https://api.postpeer.dev/documentation/json)
- [Privacy policy](https://postpeer.dev/privacy)
- [Terms of service](https://postpeer.dev/terms)
- [Support](https://postpeer.dev/contact)

REST, SDK, and OpenAPI material is provided only as developer reference. The plugin's automatic execution path for live PostPeer operations is the declared hosted MCP server.

## Install

In Claude Code, run:

```text
/plugin install postpeer-skills --marketplace PostPeer-API/skills
```

Claude then prompts for your PostPeer access key. Once Anthropic lists the plugin in the Claude community marketplace, it can also be installed from Claude's plugin UI.

## Maintainer validation

Before release, maintainers should run the repository's JSON and plugin validators and review every credential- and execution-related text match. Live tests should use a non-production PostPeer project and test social accounts. Do not use production recipients, accounts, or content for release testing.
