# PostPeer Skills

PostPeer Skills is a Claude Code plugin for publishing, scheduling, analyzing, and managing social media posts through [PostPeer](https://postpeer.dev). It supports TikTok, Instagram, YouTube, LinkedIn, Facebook, X, Pinterest, Threads, Bluesky, and Google Business Profile.

The skills use the PostPeer REST API by default. The hosted or npm PostPeer MCP server is optional.

## Claude Code installation and local testing

Install and authenticate [Claude Code](https://code.claude.com/docs/en/overview), then clone this repository:

```bash
git clone https://github.com/PostPeer-API/skills.git
cd skills
```

Validate the plugin before loading it:

```bash
claude plugin validate ./
claude plugin validate ./ --strict
```

Start a local Claude Code session with the plugin:

```bash
claude --plugin-dir ./
```

In that session, run `/help` to confirm that the five `postpeer-skills:*` commands appear. Then try a read-only request:

```text
/postpeer-skills:postpeer-api Show me how to check PostPeer API health.
```

Run `/reload-plugins` after editing a skill.

## Configure `POSTPEER_API_KEY`

Create an access key in the [PostPeer dashboard](https://postpeer.dev/dashboard). Never commit the key. This repository ignores `.env` and all `.env.*` variants except the tracked `.env.example` template.

For the current shell session, export the key before starting Claude Code:

```bash
export POSTPEER_API_KEY="your_access_key_here"
claude --plugin-dir ./
```

For local development, you may copy the template and load it into your shell:

```bash
cp .env.example .env
# Edit .env and set POSTPEER_API_KEY.
set -a
source .env
set +a
claude --plugin-dir ./
```

The REST API sends the key in the `x-access-key` header. The optional MCP server uses the same key as a bearer token. Do not paste real keys into prompts, logs, examples, or committed files.

## Available Claude Code commands

| Command | Use it to |
| --- | --- |
| [`/postpeer-skills:postpeer-api`](skills/postpeer-api/SKILL.md) | Plan raw REST, curl, SDK, OpenAPI, or MCP work |
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

Publishing, account disconnection, and notification changes affect external systems. Review the target accounts, content, time zone, and destination before approving those actions.

## External services and permissions

| Service or permission | Requirement |
| --- | --- |
| PostPeer account and API key | Required for authenticated API operations |
| Outbound HTTPS to `api.postpeer.dev` | Required for REST calls and media-upload setup |
| Social-platform authorization | Required only for each platform you connect or publish to; OAuth completes in the user's browser, while Bluesky requires an app password rather than the main password |
| Local file read and upload | Required only when publishing local media; PostPeer returns a temporary upload URL |
| Webhook endpoint or email recipient | Required only for lifecycle notifications; webhook destinations must use HTTPS |
| PostPeer MCP and outbound HTTPS to `mcp.postpeer.dev` | Optional alternative to REST |
| Node.js, npm registry access, and `postpeer-mcp` | Optional when running the npm MCP server |

The REST API schema is published at [api.postpeer.dev/documentation/json](https://api.postpeer.dev/documentation/json).

### Optional MCP configuration

Hosted MCP:

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
export POSTPEER_API_KEY="your_access_key_here"
npx -y postpeer-mcp@latest
```

## Other agent runtimes

To install all five standalone skills with [skills.sh](https://skills.sh):

```bash
npx skills add PostPeer-API/skills --all
```

Standalone installations use bare commands such as `/schedule-social-post`. Claude Code plugin installations use the namespaced commands listed above.

## Community marketplace installation

> **Available only after Anthropic approves and lists this plugin in the Claude Code community marketplace.**

After approval, add the community marketplace and install the plugin from inside Claude Code:

```text
/plugin marketplace add anthropics/claude-plugins-community
/plugin install postpeer-skills@claude-community
```

If the install summary asks you to reload, run `/reload-plugins`.

## Manual live tests

The validator and `--plugin-dir` smoke test do not call PostPeer. Before release, run these tests with a non-production PostPeer project and test social accounts:

1. Check `GET /v1/health/auth` and list integrations.
2. Generate an OAuth URL without completing an unintended account connection.
3. Save a draft, schedule a test post, reschedule it, and cancel it.
4. Retrieve analytics for a known post or connected account.
5. Send a test notification to a controlled HTTPS webhook, then create and delete a test subscription.
6. Upload disposable media and confirm that the returned public URL works.

Do not use production accounts, recipients, or content for these tests.
