# AutoManus MCP Server

> Create AI sales agents instantly from Claude Desktop, Cursor, or any MCP-compatible AI tool.

[![npm version](https://badge.fury.io/js/%40automanus%2Fmcp-server.svg)](https://www.npmjs.com/package/@automanus/mcp-server)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## Overview

The AutoManus MCP Server integrates with Claude Desktop, Cursor, Claude Code, and other Model Context Protocol (MCP) compatible tools. It enables you to create and manage AI sales agents using natural language - no coding required.

**Key Capabilities:**
- Create AI sales agents from a company name and website
- Auto-research websites to populate knowledge base
- Deploy instantly to WhatsApp and Webchat
- Add custom knowledge base items
- Check agent status and deployment info

## Installation

### Claude Desktop

Add to your Claude Desktop config file:

| OS | Config Location |
|----|-----------------|
| **macOS** | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| **Windows** | `%APPDATA%\Claude\claude_desktop_config.json` |

```json
{
  "mcpServers": {
    "automanus": {
      "command": "npx",
      "args": ["-y", "@automanus/mcp-server"]
    }
  }
}
```

Restart Claude Desktop after saving.

### Cursor

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "automanus": {
      "command": "npx",
      "args": ["-y", "@automanus/mcp-server"]
    }
  }
}
```

### Claude Code (CLI)

Add to `~/.claude/mcp.json`:

```json
{
  "mcpServers": {
    "automanus": {
      "command": "npx",
      "args": ["-y", "@automanus/mcp-server"]
    }
  }
}
```

## Usage

Once installed, just talk to your AI assistant naturally:

### Create an Agent

```
"Create an AI sales agent for Stripe"
```

```
"Build a customer service chatbot for my company TechCo using techco.com"
```

```
"I need a sales bot for my cleaning business at sparkclean.com"
```

### What Happens

1. Claude asks for your email (to send the agent claim link)
2. AutoManus researches the website automatically
3. Creates the agent with populated knowledge base
4. Deploys to WhatsApp and Webchat
5. Sends you an email with links to manage everything

## Authentication

### Default: Email-Based (No Config Required)

Claude will ask for your email when creating an agent. A claim link is sent to that email.

### Optional: Pre-configure Email

Skip the email prompt by adding to your config:

```json
{
  "mcpServers": {
    "automanus": {
      "command": "npx",
      "args": ["-y", "@automanus/mcp-server"],
      "env": {
        "AUTOMANUS_EMAIL": "your-email@example.com"
      }
    }
  }
}
```

### Optional: API Key (For Existing Users)

With an API key, agents are created directly under your account:

```json
{
  "mcpServers": {
    "automanus": {
      "command": "npx",
      "args": ["-y", "@automanus/mcp-server"],
      "env": {
        "AUTOMANUS_API_KEY": "ak_your_api_key_here"
      }
    }
  }
}
```

Get your API key from [automanus.io/dashboard/settings/api](https://automanus.io/dashboard/settings/api).

## Available Tools

### create_sales_agent

Create an AI sales agent for a business.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `email` | string | Yes | Email for account association |
| `company_name` | string | Yes | Business name |
| `website_url` | string | No | Website to research for knowledge base |

**Example prompt:**
```
"Create a sales agent for Acme Corp at acme.com, use my email john@acme.com"
```

### add_knowledge

Add a knowledge base item to an existing agent.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `agent_id` | string | Yes | Agent UUID (from create_sales_agent) |
| `title` | string | Yes | Item title |
| `content` | string | Yes | Item content |
| `item_type` | string | No | `faq`, `product`, `policy`, or `document` |

**Example prompt:**
```
"Add a FAQ to agent abc-123: Title 'Pricing' Content 'Our Pro plan is $99/month'"
```

### get_agent_status

Get status of an agent including deployment info and knowledge base count.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `agent_id` | string | Yes | Agent UUID |

**Example prompt:**
```
"What's the status of agent abc-123?"
```

## What You Get

When you create an agent, you receive:

| Feature | Description |
|---------|-------------|
| **AI Sales Agent** | Trained on the company's website content |
| **WhatsApp Link** | Shareable link for customers to chat |
| **Webchat Widget** | Embed code for your website |
| **Knowledge Base** | Auto-populated from website research |
| **Dashboard Access** | Manage, train, and customize your agent |

## Rate Limits

| Auth Method | Limit |
|-------------|-------|
| Email (no API key) | 10 agents per email per day |
| API Key | Based on your plan |

## Troubleshooting

### Tools not appearing in Claude

1. Restart Claude Desktop / Cursor after updating config
2. Verify `npx` is in your system PATH
3. Check the config file is valid JSON

### Rate limit errors

- Maximum 10 agents per email per day without API key
- Use an API key for higher limits

### Website research failing

Some websites block automated access. Solutions:
1. Try a different page URL
2. Use `add_knowledge` to add content manually
3. Provide content in your prompt using `codebase_context` parameter

## Links

- [npm Package](https://www.npmjs.com/package/@automanus/mcp-server)
- [GitHub Repository](https://github.com/automanus-io/mcp-server)
- [API Reference](../api/README.md)
- [Dashboard](https://automanus.io/dashboard)

## Support

- **Email**: hello@automanus.io
- **GitHub Issues**: [Report a bug](https://github.com/automanus-io/mcp-server/issues)

## License

MIT
