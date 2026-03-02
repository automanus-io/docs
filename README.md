# AutoManus Documentation

> Build and deploy AI sales agents to WhatsApp and Webchat in seconds.

[![Website](https://img.shields.io/badge/Website-automanus.io-blue)](https://automanus.io)
[![npm](https://img.shields.io/npm/v/@automanus/mcp-server)](https://www.npmjs.com/package/@automanus/mcp-server)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## What is AutoManus?

AutoManus is a platform for creating AI-powered sales and customer service agents that deploy instantly to:
- **WhatsApp** - Customers can chat via WhatsApp link
- **Webchat** - Embeddable widget for your website

Agents are trained on your company's website and knowledge base, providing accurate, contextual responses 24/7.

## Quick Links

| Resource | Description |
|----------|-------------|
| [MCP Server Guide](./mcp/README.md) | Install the MCP server for Claude Desktop, Cursor, Claude Code |
| [API Reference](./api/README.md) | REST API for programmatic agent creation |
| [Examples](./examples/README.md) | Configuration examples and code snippets |
| [Dashboard](https://automanus.io/dashboard) | Manage your agents |

## Getting Started

### Option 1: MCP Server (Recommended)

Create agents directly from Claude Desktop, Cursor, or Claude Code:

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

Then just say: *"Create an AI sales agent for Acme Corp using acme.com"*

[Full MCP Setup Guide →](./mcp/README.md)

### Option 2: REST API

Create agents programmatically:

```bash
curl -X POST https://automanus.io/api/v1/public/agents \
  -H "Content-Type: application/json" \
  -d '{
    "email": "you@company.com",
    "company_name": "Acme Corp",
    "website_url": "https://acme.com"
  }'
```

[Full API Reference →](./api/README.md)

## Features

- **Instant Deployment** - Agents go live immediately on WhatsApp and Webchat
- **Auto Website Research** - Automatically scrapes and learns from your website
- **Knowledge Base** - Add custom FAQs, product info, and policies
- **No Code Required** - Works entirely through natural language or simple API calls
- **Free Tier** - 100 free message credits to get started

## Pricing

| Plan | Credits/Month | Features |
|------|---------------|----------|
| **Free** | 100 | WhatsApp + Webchat, Basic KB |
| **Starter** | 1,000 | Priority support, Analytics |
| **Pro** | 5,000 | Custom branding, API access, Advanced analytics |

## Support

- **Documentation**: [automanus.io/docs](https://automanus.io/docs)
- **Email**: hello@automanus.io
- **GitHub Issues**: [Report a bug](https://github.com/automanus-io/docs/issues)

## License

MIT - See [LICENSE](./LICENSE) for details.
