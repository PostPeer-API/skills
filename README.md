# PostPeer Skills

Workflow-first skills for AI agents using [PostPeer](https://postpeer.dev) to publish, schedule, analyze, and manage social media posts through the PostPeer MCP/API.

These skills do not add a separate CLI. PostPeer already exposes the execution layer through hosted MCP, the `postpeer-mcp` npm package, the REST API at `https://api.postpeer.dev`, and the `@postpeer/node` SDK.

## Install All PostPeer Skills

```bash
npx skills add PostPeer-API/skills --all
```

Use `--all` so users get the full PostPeer skill set without an interactive picker. After install, the individual skills are available for focused slash-style usage such as `/schedule-social-post`, `/analytics-report`, and `/connect-social-accounts`.

Set a PostPeer access key in your MCP client or environment. Get one from [postpeer.dev/dashboard](https://postpeer.dev/dashboard).

Hosted MCP config:

```json
{
  "mcpServers": {
    "postpeer": {
      "url": "https://mcp.postpeer.dev/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_POSTPEER_ACCESS_KEY"
      }
    }
  }
}
```

Local npm MCP:

```bash
export POSTPEER_API_KEY="YOUR_POSTPEER_ACCESS_KEY"
npx -y postpeer-mcp@latest
```

REST API:

```bash
export POSTPEER_API_KEY="YOUR_POSTPEER_ACCESS_KEY"

curl -s "https://api.postpeer.dev/v1/connect/integrations" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

OpenAPI spec:

```text
https://api.postpeer.dev/documentation/json
```

## Available Skills

Install with `--all` so users receive every skill in one command.

| Skill | Use it when you want to... | Output |
|---|---|---|
| [`postpeer-api`](skills/postpeer-api/) | Route raw PostPeer MCP/API/curl/SDK tasks and understand platform rules | Correct tool/API plan, auth and prerequisite guidance |
| [`schedule-social-post`](skills/schedule-social-post/) | Publish immediately or schedule a post to one or more platforms | Valid `create_post` payload or MCP/API tool call |
| [`connect-social-accounts`](skills/connect-social-accounts/) | Connect or inspect social integrations | OAuth, Bluesky, profile, and integration steps |
| [`analytics-report`](skills/analytics-report/) | Report on post or account performance | Metrics summary with caveats and next actions |
| [`webhook-notifications`](skills/webhook-notifications/) | Set up post lifecycle alerts | Webhook/email notification plan and tool calls |

## Example Prompts

```text
Schedule this video to TikTok and Instagram tomorrow at 9 AM New York time.
```

```text
Connect a customer's LinkedIn and YouTube accounts, then list their connected integrations.
```

```text
Set up failed-publish webhook alerts for one customer profile.
```

```text
Get analytics for last month's LinkedIn and TikTok posts and summarize what performed best.
```

## How the Skills Work Together

```text
postpeer-api
  |- schedule-social-post
  |- connect-social-accounts
  |- analytics-report
  `- webhook-notifications
```

Use `postpeer-api` as the shared routing and hard-rules layer. The workflow skills should produce useful outcomes, not raw endpoint dumps.

## Design Principles

1. Treat `https://api.postpeer.dev/documentation/json` as the REST source of truth.
2. Workflow first, API second.
3. Prefer MCP tools when available.
4. Do not invent platform settings. Discover required values from PostPeer tools or OpenAPI.
5. Upload local media only when needed; use existing public URLs directly.
6. Respect platform prerequisites, especially TikTok creator info and Pinterest board IDs.
7. Use profiles to group end-user or customer integrations.
8. Use notifications for lifecycle events instead of polling by default.
