# PostPeer Skills

Workflow-first skills for AI agents using [PostPeer](https://postpeer.dev) to publish, schedule, analyze, and manage social media posts across TikTok, Instagram, YouTube, LinkedIn, Facebook, X, Pinterest, Threads, and Bluesky.

The skills work standalone against the PostPeer REST API. All you need is a `POSTPEER_API_KEY`. No MCP server, no extra install, no CLI. If your agent also has the PostPeer MCP configured, the skills can use those tools instead, but MCP is entirely optional.

## Install All PostPeer Skills

```bash
npx skills add PostPeer-API/skills --all
```

Use `--all` so you get the full PostPeer skill set without an interactive picker. After install, the individual skills are available for focused slash-style usage such as `/schedule-social-post`, `/analytics-report`, and `/connect-social-accounts`.

Prefer to clone instead:

```bash
git clone https://github.com/PostPeer-API/skills.git
```

## Set Your API Key

Copy `.env.example` to `.env` and fill in your key:

```bash
cp .env.example .env
```

```bash
POSTPEER_API_KEY=your_access_key_here
```

Get a key from the [PostPeer dashboard](https://postpeer.dev/dashboard). The same key works for REST and MCP.

REST API (the default, no MCP needed). The skills attach the key as the `x-access-key` header:

```bash
export POSTPEER_API_KEY="your_access_key_here"

curl -s "https://api.postpeer.dev/v1/connect/integrations" \
  -H "x-access-key: $POSTPEER_API_KEY"
```

OpenAPI spec: `https://api.postpeer.dev/documentation/json`.

## Optional: Use MCP

The skills do not require MCP. If you prefer a tool-calling client, PostPeer also runs a hosted MCP server. Point your client at it with the same key:

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

Or run the npm MCP locally:

```bash
export POSTPEER_API_KEY="your_access_key_here"
npx -y postpeer-mcp@latest
```

## Available Skills

Install with `--all` so users receive every skill in one command.

| Skill | Use it when you want to... | Output |
|---|---|---|
| [`postpeer-api`](skills/postpeer-api/) | Route raw PostPeer REST/curl/SDK/MCP tasks and understand platform rules | Correct API plan, auth and prerequisite guidance |
| [`schedule-social-post`](skills/schedule-social-post/) | Publish immediately or schedule a post to one or more platforms | Valid REST `POST /v1/posts` call (or MCP `create_post`) |
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
3. REST is the default and works on its own. Use the PostPeer MCP tools only if a client already has them configured.
4. Do not invent platform settings. Discover required values from PostPeer tools or OpenAPI.
5. Upload local media only when needed; use existing public URLs directly.
6. Respect platform prerequisites, especially TikTok creator info and Pinterest board IDs.
7. Use profiles to group end-user or customer integrations.
8. Use notifications for lifecycle events instead of polling by default.
