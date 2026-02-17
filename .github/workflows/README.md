# GitHub Actions Webhook Monitor

This workflow provides monitoring and alerting for the Nexus webhook-based agent chaining system.

## Overview

The webhook monitor runs automatically whenever a GitHub issue comment is created. It:

1. **Validates webhook processing** - Checks if copilot mentioned an agent
2. **Health checks** - Verifies the webhook server is responding
3. **Alerts on failure** - Sends Telegram notification if webhook server is down
4. **Audit trail** - Creates searchable logs in GitHub Actions UI

## Architecture

```
GitHub Issue Comment (copilot @agent)
         │
         ├─→ Webhook Server (primary path - instant)
         │   └─→ Agent Launcher
         │
         └─→ GitHub Actions (monitoring path - audit)
             └─→ Health Check → Telegram Alert (if down)
```

## Required Secrets

Configure these in your GitHub repository settings (`Settings > Secrets and variables > Actions`):

### `WEBHOOK_URL`
**Required:** Yes  
**Format:** `http://your-server-ip:8081` or `https://webhook.yourdomain.com`  
**Purpose:** URL of your nexus-webhook service for health checks  

**Example:**
```
http://10.2.1.102:8081
```

### `TELEGRAM_BOT_TOKEN`
**Required:** Optional (needed for alerts)  
**Format:** `<bot-id>:<token>`  
**Purpose:** Bot token for sending alerts  

**Example:**
```
1234567890:REDACTED_TELEGRAM_BOT_TOKEN
```

### `TELEGRAM_CHAT_ID`
**Required:** Optional (needed for alerts)  
**Format:** Numeric chat/user ID  
**Purpose:** Telegram chat to receive alerts  

**Example:**
```
47168736
```

## How It Works

### Trigger Conditions

The workflow runs when:
- An issue comment is created
- The comment author is `copilot`
- The comment contains an `@AgentName` mention

### Workflow Steps

1. **Check for Agent Mention**
   - Parses comment body for `@AgentName` pattern
   - Extracts agent name for logging

2. **Webhook Health Check**
   - Calls `GET /health` endpoint on webhook server
   - Expects HTTP 200 response
   - Timeout: 5 seconds

3. **Alert on Failure**
   - If webhook returns non-200 or times out
   - Sends Telegram message with issue details
   - Continues workflow (doesn't block)

4. **Logging**
   - Records event in GitHub Actions logs
   - Includes: issue number, agent name, webhook status, timestamp
   - Searchable via Actions UI

## Viewing Logs

1. Go to your repository on GitHub
2. Click **Actions** tab
3. Click **Webhook Monitor** workflow
4. Click on a specific run to see details

## Benefits

### 1. **Redundancy**
- If webhook server crashes, you get immediate notification
- Doesn't interfere with normal webhook operation

### 2. **Audit Trail**
- Every agent chaining event logged in GitHub
- Searchable history of when agents were triggered
- Useful for debugging and compliance

### 3. **Zero Maintenance**
- Runs on GitHub-hosted runners (no server cost)
- Automatically updates with repository

### 4. **Observability**
- See webhook health over time
- Identify patterns in agent chaining
- Monitor server uptime

## Limitations

- **Not a replacement** - This monitors webhooks, doesn't replace them
- **1-2 minute delay** - GitHub Actions startup takes ~30-60s
- **No agent execution** - Only monitors, doesn't launch agents

## Troubleshooting

### Workflow not running?

Check:
1. Workflow file is in `.github/workflows/` directory
2. Repository has Actions enabled
3. Comment author is exactly `copilot`

### Health check failing?

Check:
1. `WEBHOOK_URL` secret is set correctly
2. Webhook server is accessible from internet (if using GitHub-hosted runners)
3. Firewall/security group allows inbound traffic on webhook port

### No Telegram alerts?

Check:
1. `TELEGRAM_BOT_TOKEN` secret is set
2. `TELEGRAM_CHAT_ID` secret is set
3. Bot has permission to message the chat

## Future Enhancements

Possible additions:
- Check audit.log file via API to confirm agent launched
- Post GitHub comment if webhook failed
- Retry webhook delivery if server was down
- Metrics dashboard with workflow run history
