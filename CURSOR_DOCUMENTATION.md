# Cursor - Documentation

This document contains all documentation extracted from Cursor official documentation and API references.

**Last Updated:** November 12, 2025

**Source:** https://cursor.com/docs/

**Documentation:** https://cursor.com/docs/

---

## Table of Contents

1. [Introduction](#introduction)
2. [Getting Started](#getting-started)
3. [Core Features](#core-features)
4. [APIs](#apis)
5. [Configuration](#configuration)
6. [Integrations](#integrations)
7. [Best Practices](#best-practices)
8. [Troubleshooting](#troubleshooting)

---

## Introduction

Cursor is an AI-powered code editor that understands your codebase and helps you code faster through natural language. Just describe what you want to build or change and Cursor will generate the code for you.

### Key Features

- **AI-Powered Autocomplete**: Tab model for multi-line code suggestions
- **Inline Edit**: Edit code selections with natural language
- **Agent**: AI assistant that can create files, run tests, and perform complex tasks
- **Codebase Understanding**: Deep understanding of your entire codebase
- **Multiple Models**: Support for Claude, GPT, Gemini, and more
- **Model Context Protocol (MCP)**: Extensible architecture for integrations

### Available Models

| Name | Default Context | Max Mode | Capabilities |
| --- | --- | --- | --- |
| Claude 4.1 Opus | - | 200k | AgentThinking |
| Claude 4.5 Sonnet | 200k | 1M | AgentThinking |
| Composer 1 | 200k | - | AgentThinking |
| Gemini 2.5 Pro | 200k | 1M | AgentThinking |
| GPT-5 | 272k | - | AgentThinking |
| GPT-5 Fast | 272k | - | AgentThinking |
| GPT-5-Codex | 272k | - | AgentThinking |
| Grok Code | 256k | - | AgentThinking |

---

## Getting Started

### Installation

1. Download Cursor from https://cursor.com/downloads
2. Install the application
3. Sign in or create an account

### Quickstart

1. **Open a project in Cursor:**
   ```bash
   git clone git@github.com:voxelize/voxelize.git
   cd voxelize
   cursor .
   ```

2. **Try Tab autocomplete:**
   - Start typing: `function calculate`
   - Press Tab to accept suggestions
   - Cursor suggests parameters and function bodies

3. **Use Inline Edit:**
   - Select code
   - Press `Cmd K` (Mac) or `Ctrl K` (Windows/Linux)
   - Type your instruction
   - Press Enter to apply

4. **Chat with Agent:**
   - Open Chat panel: `Cmd I` (Mac) or `Ctrl I` (Windows/Linux)
   - Ask: "Add tests for this function and run them"
   - Agent creates test files and runs them

---

## Core Features

### Tab (Autocomplete)

Tab is the autocomplete model trained in-house. It provides:

- **Multi-line autocomplete**: Complete entire blocks of code
- **Cross-file suggestions**: Jump in and across files
- **Context-aware**: Understands your codebase structure

**Usage:**
- Start typing code
- Tab suggestions appear automatically
- Press Tab to accept
- Use arrow keys to navigate suggestions

### Inline Edit

Edit code selections with natural language instructions.

**Usage:**
1. Select code you want to modify
2. Press `Cmd K` (Mac) or `Ctrl K` (Windows/Linux)
3. Type your instruction in natural language
4. Press Enter to apply changes

**Example:**
```
Selection: function add(a, b) { return a + b; }
Instruction: "make this function calculate fibonacci numbers"
Result: Function is rewritten with fibonacci logic
```

### Agent

AI assistant that can perform complex tasks across your codebase.

**Capabilities:**
- Create new files
- Write tests
- Run commands
- Refactor code
- Debug issues
- Generate documentation

**Usage:**
1. Open Chat panel: `Cmd I` (Mac) or `Ctrl I` (Windows/Linux)
2. Describe what you want to accomplish
3. Agent analyzes your codebase and performs the task

**Example Commands:**
- "Add tests for this function and run them"
- "Refactor this component to use hooks"
- "Create a new API endpoint for user authentication"

### Model Context Protocol (MCP)

MCP allows Cursor to integrate with external tools and services.

**Features:**
- Extensible architecture
- Custom integrations
- Tool access for agents
- Data sources

**Setup:**
1. Navigate to Settings → MCP
2. Add MCP server configuration
3. Configure server endpoints and authentication

---

## APIs

Cursor provides multiple APIs for programmatic access to team data, AI-powered coding agents, and analytics.

### Available APIs

| API | Description | Availability |
| --- | --- | --- |
| **Admin API** | Manage team members, settings, usage data, and spending | Enterprise teams |
| **Analytics API** | Insights into team's Cursor usage, AI metrics, active users | Enterprise teams |
| **AI Code Tracking API** | Track AI-generated code contributions at commit and change levels | Enterprise teams |
| **Cloud Agents API** | Programmatically create and manage AI-powered coding agents | Beta (All Plans) |

### Authentication

All Cursor APIs use Basic Authentication.

**Basic Authentication:**
```bash
curl https://api.cursor.com/teams/members \
  -u YOUR_API_KEY:
```

**Authorization Header:**
```
Authorization: Basic {base64_encode('YOUR_API_KEY:')}
```

**Creating API Keys:**

1. **Admin API & AI Code Tracking API:**
   - Navigate to cursor.com/dashboard → Settings → Advanced → Admin API Keys
   - Click "Create New API Key"
   - Give your key a descriptive name
   - Copy the generated key immediately

2. **Analytics API:**
   - Generate from team settings page: cursor.com/settings

3. **Cloud Agents API:**
   - Create from Cursor Dashboard → Integrations

**Key Format:** `key_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`

### Rate Limits

| API | Endpoint Type | Rate Limit |
| --- | --- | --- |
| **Admin API** | Most endpoints | 20 requests/minute |
| **Admin API** | `/teams/user-spend-limit` | 60 requests/minute |
| **Analytics API** | Team-level endpoints | 100 requests/minute |
| **Analytics API** | By-user endpoints | 50 requests/minute |
| **AI Code Tracking API** | All endpoints | 20 requests/minute per endpoint |
| **Cloud Agents API** | All endpoints | Standard rate limiting |

**Rate Limit Response:**
```json
{
  "error": "Too Many Requests",
  "message": "Rate limit exceeded. Please try again later."
}
```

### Caching

Several APIs support HTTP caching with ETags.

**Supported APIs:**
- Analytics API: All endpoints
- AI Code Tracking API: All endpoints

**How Caching Works:**
1. Initial request returns `ETag` header
2. Include `ETag` value in `If-None-Match` header for subsequent requests
3. Receive `304 Not Modified` if data hasn't changed

**Example:**
```bash
# Initial request
curl -X GET "https://api.cursor.com/analytics/team/dau" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -D headers.txt

# Response includes: ETag: "abc123xyz"

# Subsequent request with ETag
curl -X GET "https://api.cursor.com/analytics/team/dau" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "If-None-Match: \"abc123xyz\""

# Returns 304 Not Modified if data hasn't changed
```

**Cache Duration:** 15 minutes (`Cache-Control: public, max-age=900`)

### Cloud Agents API

Programmatically create and manage AI-powered coding agents.

**Endpoints:**
- `GET /cloud-agent/agents` - List agents
- `GET /cloud-agent/agents/:id` - Get agent status
- `GET /cloud-agent/agents/:id/conversation` - Get agent conversation
- `POST /cloud-agent/agents` - Launch an agent
- `POST /cloud-agent/agents/:id/follow-up` - Add follow-up
- `DELETE /cloud-agent/agents/:id` - Delete agent
- `GET /cloud-agent/api-key-info` - Get API key info
- `GET /cloud-agent/models` - List models
- `GET /cloud-agent/repositories` - List GitHub repositories

**Example: Launch Agent**
```python
import requests

url = "https://api.cursor.com/cloud-agent/agents"
headers = {
    "Authorization": f"Basic {base64.b64encode(f'{API_KEY}:'.encode()).decode()}",
    "Content-Type": "application/json"
}
data = {
    "repository": "owner/repo",
    "instruction": "Add authentication to the API",
    "model": "claude-4.5-sonnet"
}

response = requests.post(url, headers=headers, json=data)
agent_id = response.json()["id"]
```

### Admin API

Manage team members, settings, usage data, and spending.

**Endpoints:**
- `GET /teams/members` - Get team members
- `GET /teams/audit-logs` - Get audit logs
- `GET /teams/daily-usage-data` - Get daily usage data
- `GET /teams/spending-data` - Get spending data
- `GET /teams/filtered-usage-events` - Get usage events data
- `POST /teams/user-spend-limit` - Set user spend limit
- `GET /teams/repo-blocklists` - Get repo blocklists
- `POST /teams/repo-blocklists` - Upsert repo blocklists
- `DELETE /teams/repo-blocklists/:id` - Delete repo blocklist

**Example: Get Team Members**
```python
import requests

url = "https://api.cursor.com/teams/members"
headers = {
    "Authorization": f"Basic {base64.b64encode(f'{API_KEY}:'.encode()).decode()}"
}

response = requests.get(url, headers=headers)
members = response.json()
```

### Analytics API

Comprehensive insights into team's Cursor usage.

**Endpoints:**
- `GET /analytics/team/agent-edits` - Agent edits
- `GET /analytics/team/tab-usage` - Tab usage
- `GET /analytics/team/dau` - Daily active users
- `GET /analytics/team/client-versions` - Client versions
- `GET /analytics/team/model-usage` - Model usage
- `GET /analytics/team/top-file-extensions` - Top file extensions
- `GET /analytics/team/leaderboard` - Leaderboard
- `GET /analytics/user/:userId/*` - By-user endpoints

**Example: Get Daily Active Users**
```python
import requests

url = "https://api.cursor.com/analytics/team/dau"
headers = {
    "Authorization": f"Basic {base64.b64encode(f'{API_KEY}:'.encode()).decode()}"
}
params = {
    "start_date": "2025-01-01",
    "end_date": "2025-01-31"
}

response = requests.get(url, headers=headers, params=params)
data = response.json()
```

### AI Code Tracking API

Track AI-generated code contributions.

**Endpoints:**
- `GET /ai-code-tracking/commits` - Get commit metrics (JSON, paginated)
- `GET /ai-code-tracking/commits/csv` - Download commit metrics (CSV, streaming)
- `GET /ai-code-tracking/changes` - Get code changes (JSON, paginated)
- `GET /ai-code-tracking/changes/csv` - Download code changes (CSV, streaming)

**Example: Get Commit Metrics**
```python
import requests

url = "https://api.cursor.com/ai-code-tracking/commits"
headers = {
    "Authorization": f"Basic {base64.b64encode(f'{API_KEY}:'.encode()).decode()}"
}
params = {
    "start_date": "2025-01-01",
    "end_date": "2025-01-31",
    "page": 1,
    "per_page": 50
}

response = requests.get(url, headers=headers, params=params)
commits = response.json()
```

---

## Configuration

### Extensions

Cursor supports VS Code extensions. Install extensions from:
- VS Code Marketplace
- Cursor Extension Marketplace

**Install Extension:**
1. Open Command Palette: `Cmd Shift P` (Mac) or `Ctrl Shift P` (Windows/Linux)
2. Type "Extensions: Install Extensions"
3. Search and install desired extensions

### Keyboard Shortcuts

**Core Shortcuts:**
- `Tab` - Accept autocomplete suggestion
- `Cmd K` / `Ctrl K` - Inline Edit
- `Cmd I` / `Ctrl I` - Open Chat (Agent)
- `Cmd Shift P` / `Ctrl Shift P` - Command Palette
- `Cmd ,` / `Ctrl ,` - Open Settings

**Customization:**
1. Open Settings: `Cmd ,` (Mac) or `Ctrl ,` (Windows/Linux)
2. Navigate to Keyboard Shortcuts
3. Search for command
4. Click to rebind

### Themes

Cursor supports VS Code themes.

**Change Theme:**
1. Open Command Palette: `Cmd Shift P` / `Ctrl Shift P`
2. Type "Preferences: Color Theme"
3. Select desired theme

### Shell Commands

Configure shell commands for Agent to execute.

**Setup:**
1. Open Settings
2. Navigate to Shell Commands
3. Add command aliases and paths

---

## Integrations

### GitHub

**Features:**
- Repository access
- Code indexing
- Pull request integration
- Commit tracking

**Setup:**
1. Navigate to Settings → Integrations
2. Connect GitHub account
3. Authorize repository access

### Git

**Features:**
- Git operations
- Commit tracking
- Branch management
- Diff viewing

### Linear

**Features:**
- Issue tracking
- Task management
- Project integration

**Setup:**
1. Navigate to Settings → Integrations
2. Connect Linear account
3. Configure project mappings

### Slack

**Features:**
- Notifications
- Team updates
- Activity sharing

**Setup:**
1. Navigate to Settings → Integrations
2. Connect Slack workspace
3. Configure notification channels

### Deeplinks

Create deep links to specific files, lines, or conversations.

**Format:**
```
cursor://file?path=/path/to/file&line=10
cursor://chat?conversation=abc123
```

---

## Best Practices

### 1. Implement Exponential Backoff

When you receive a 429 response, wait before retrying with increasing delays:

```python
import time
import requests

def make_request_with_backoff(url, headers, max_retries=5):
    for attempt in range(max_retries):
        response = requests.get(url, headers=headers)

        if response.status_code == 429:
            # Exponential backoff: 1s, 2s, 4s, 8s, 16s
            wait_time = 2 ** attempt
            print(f"Rate limited. Waiting {wait_time}s before retry...")
            time.sleep(wait_time)
            continue

        return response

    raise Exception("Max retries exceeded")
```

### 2. Distribute Requests Over Time

Spread API calls over time rather than making burst requests:
- Schedule batch jobs at different intervals
- Add delays between requests when processing large datasets
- Use queuing systems to smooth traffic spikes

### 3. Leverage Caching

Use HTTP caching with ETags for Analytics API and AI Code Tracking API:
- Reduces bandwidth usage
- Faster responses when data hasn't changed
- Doesn't count against rate limits (for 304 responses)

Use date shortcuts (`7d`, `30d`) instead of timestamps for better caching support.

### 4. Monitor Your Usage

Track request patterns to stay within limits:
- Log API call timestamps and response codes
- Set up alerts for 429 responses
- Monitor daily/weekly usage trends
- Adjust polling intervals based on actual needs

### 5. Batch Wisely

For endpoints with pagination:
- Use appropriate page sizes to get more data per request
- For Analytics API by-user endpoints: Use `users` parameter to filter specific users
- For large data extractions: Use CSV endpoints when available

### 6. Poll at Appropriate Intervals

Don't over-poll endpoints that update infrequently:
- **Admin API** `/teams/daily-usage-data`: Poll at most once per hour
- **Admin API** `/teams/filtered-usage-events`: Poll at most once per hour
- **Analytics API**: Use date shortcuts (`7d`, `30d`) for better caching
- **AI Code Tracking API**: Poll every few minutes (data ingested in near real-time)

### 7. Handle Errors Gracefully

Implement proper error handling for all API calls:

```python
async def fetch_analytics(endpoint):
    try:
        response = await fetch(f"https://api.cursor.com{endpoint}", {
            "headers": {
                "Authorization": f"Basic {base64.b64encode(f'{API_KEY}:'.encode()).decode()}"
            }
        })

        if response.status == 429:
            raise Exception("Rate limit exceeded")
        if response.status == 401:
            raise Exception("Authentication failed")
        if response.status == 403:
            raise Exception("Enterprise access required")
        if not response.ok:
            raise Exception(f"API error: {response.status}")

        return await response.json()
    except Exception as error:
        print(f"API request failed: {error}")
        raise
```

---

## Troubleshooting

### Common Issues

#### API Returns 401 Unauthorized

**Solution:**
1. Verify API key is correct
2. Check that API key hasn't expired
3. Ensure Basic Authentication is properly formatted
4. Verify API key has required permissions

#### API Returns 403 Forbidden

**Solution:**
1. Check if feature requires Enterprise plan
2. Verify team has Enterprise access
3. Contact support if issue persists

#### API Returns 429 Too Many Requests

**Solution:**
1. Implement exponential backoff
2. Reduce request frequency
3. Use caching to avoid unnecessary requests
4. Distribute requests over time

#### Agent Not Responding

**Solution:**
1. Check internet connection
2. Verify model is available
3. Check context window limits
4. Try different model
5. Restart Cursor

#### Tab Autocomplete Not Working

**Solution:**
1. Check if Tab is enabled in settings
2. Verify model is selected
3. Check context window usage
4. Restart Cursor

### Getting a Request ID

For support requests, include a Request ID:

1. Open Developer Tools: `Cmd Option I` (Mac) or `Ctrl Shift I` (Windows/Linux)
2. Navigate to Network tab
3. Find the failed request
4. Copy Request ID from response headers

### Common Error Responses

**400 Bad Request:**
```json
{
  "error": "Bad Request",
  "message": "Some users are not in the team"
}
```

**401 Unauthorized:**
```json
{
  "error": "Unauthorized",
  "message": "Invalid API key"
}
```

**403 Forbidden:**
```json
{
  "error": "Forbidden",
  "message": "Enterprise access required"
}
```

**404 Not Found:**
```json
{
  "error": "Not Found",
  "message": "Resource not found"
}
```

**429 Too Many Requests:**
```json
{
  "error": "Too Many Requests",
  "message": "Rate limit exceeded. Please try again later."
}
```

**500 Internal Server Error:**
```json
{
  "error": "Internal Server Error",
  "message": "An unexpected error occurred"
}
```

---

## Additional Resources

- **Cursor Documentation:** https://cursor.com/docs/
- **Cursor Dashboard:** https://cursor.com/dashboard
- **Cursor Forum:** https://forum.cursor.com/
- **Cursor Support:** hi@cursor.com
- **Cursor Changelog:** https://www.cursor.com/changelog
- **AI Foundations Course:** https://cursor.com/learn

---

*End of Documentation*








