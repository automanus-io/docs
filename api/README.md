# AutoManus REST API

> Programmatically create and manage AI sales agents.

**Base URL:** `https://automanus.io/api/v1/public`

## Overview

The AutoManus API allows you to:
- Create AI sales agents with automatic website research
- Add knowledge base items to train your agents
- Check agent status and deployment info

## Authentication

### API Key (Recommended)

For authenticated requests, include your API key in the Authorization header:

```
Authorization: Bearer ak_your_api_key_here
```

Get your API key from [automanus.io/dashboard](https://automanus.io/dashboard) → Settings → API Keys.

### Email-Only (No API Key)

For quick testing, you can create agents with just an email address. A claim link will be sent to verify ownership.

## Endpoints

### POST /agents

Create a new AI sales agent.

**URL:** `https://automanus.io/api/v1/public/agents`

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `email` | string | Yes* | Email for account association. Required if no API key. |
| `company_name` | string | Yes | Business name for the agent |
| `website_url` | string | No | Website to research for knowledge base |
| `codebase_context` | string | No | Additional context to include in knowledge base |

*Email is optional if using API key authentication.

#### Example Request

```bash
curl -X POST https://automanus.io/api/v1/public/agents \
  -H "Content-Type: application/json" \
  -d '{
    "email": "you@company.com",
    "company_name": "Acme Corp",
    "website_url": "https://acme.com"
  }'
```

With API key:

```bash
curl -X POST https://automanus.io/api/v1/public/agents \
  -H "Authorization: Bearer ak_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "company_name": "Acme Corp",
    "website_url": "https://acme.com"
  }'
```

#### Success Response (200)

```json
{
  "success": true,
  "agent_id": "550e8400-e29b-41d4-a716-446655440000",
  "agent_name": "Acme Assistant",
  "company_name": "Acme Corp",
  "webchat_embed_code": "<script src=\"https://automanus.io/widget/automanus-chat.js\" data-widget-key=\"wc_abc123\"></script>",
  "webchat_widget_key": "wc_abc123",
  "whatsapp_link": "https://wa.me/16506053956?text=Hi!%20I'd%20like%20to%20talk%20to%20Acme%20Corp",
  "is_new_user": true,
  "claim_url": "https://automanus.io/login?invite=token-here",
  "dashboard_url": "https://automanus.io/dashboard/ai-agent",
  "trial_info": {
    "expires_in_days": null,
    "message_limit": 100
  },
  "knowledge_base_count": 5
}
```

#### Duplicate Agent Response (409)

If an agent already exists for the same website:

```json
{
  "success": false,
  "error": "An agent already exists for this website",
  "existing_agent": {
    "agent_id": "550e8400-e29b-41d4-a716-446655440000",
    "agent_name": "Acme Assistant",
    "company_name": "Acme Corp"
  },
  "message": "Use the add_knowledge tool to add information to the existing agent."
}
```

---

### POST /knowledge

Add a knowledge base item to an existing agent.

**URL:** `https://automanus.io/api/v1/public/knowledge`

#### Request Body

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `agent_id` | string | Yes | UUID of the agent |
| `title` | string | Yes | Title of the knowledge item |
| `content` | string | Yes | Content/body of the knowledge item |
| `item_type` | string | No | `faq`, `product`, `policy`, or `document` |
| `category` | string | No | Category name (e.g., "Pricing", "Support") |

#### Example Request

```bash
curl -X POST https://automanus.io/api/v1/public/knowledge \
  -H "Authorization: Bearer ak_your_api_key" \
  -H "Content-Type: application/json" \
  -d '{
    "agent_id": "550e8400-e29b-41d4-a716-446655440000",
    "title": "Pricing FAQ",
    "content": "Our Pro plan starts at $99/month with unlimited users.",
    "item_type": "faq",
    "category": "Pricing"
  }'
```

#### Success Response (200)

```json
{
  "success": true,
  "knowledge_item_id": "660e8400-e29b-41d4-a716-446655440001",
  "agent_id": "550e8400-e29b-41d4-a716-446655440000",
  "agent_name": "Acme Assistant",
  "embedding_generated": true
}
```

#### Authentication Notes

- **With API key**: You can add knowledge to any agent you own
- **Without API key**: Only works for demo agents (created via email flow, not yet claimed)

---

### GET /agents/:agent_id/status

Get agent status and deployment info.

**URL:** `https://automanus.io/api/v1/public/agents/:agent_id/status`

#### Example Request

```bash
curl https://automanus.io/api/v1/public/agents/550e8400-e29b-41d4-a716-446655440000/status \
  -H "Authorization: Bearer ak_your_api_key"
```

#### Success Response (200)

```json
{
  "success": true,
  "agent_id": "550e8400-e29b-41d4-a716-446655440000",
  "agent_name": "Acme Assistant",
  "company_name": "Acme Corp",
  "is_active": true,
  "knowledge_base_count": 12,
  "whatsapp_enabled": true,
  "webchat_enabled": true,
  "created_at": "2024-01-15T10:30:00Z"
}
```

---

## Error Codes

| Code | Error | Description |
|------|-------|-------------|
| 400 | `Missing required fields` | Required parameters not provided |
| 400 | `Invalid email format` | Email validation failed |
| 401 | `Invalid API key` | API key authentication failed |
| 403 | `Permission denied` | You don't own this agent |
| 404 | `Agent not found` | Agent ID doesn't exist |
| 409 | `Agent already exists` | Duplicate agent for this website |
| 429 | `Rate limit exceeded` | Too many requests |
| 500 | `Internal server error` | Server error, please retry |

## Rate Limits

| Auth Method | Agents/Day | KB Items/Hour |
|-------------|------------|---------------|
| Email only | 10 | 50 per agent |
| API Key (Free) | 10 | 50 per agent |
| API Key (Starter) | 100 | 200 per agent |
| API Key (Pro) | Unlimited | 500 per agent |

## Webhooks (Coming Soon)

Subscribe to events:
- `agent.created` - New agent created
- `agent.message` - Agent received a message
- `agent.knowledge.added` - Knowledge item added

## Code Examples

### Node.js

```javascript
const createAgent = async () => {
  const response = await fetch('https://automanus.io/api/v1/public/agents', {
    method: 'POST',
    headers: {
      'Authorization': 'Bearer ak_your_api_key',
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      company_name: 'Acme Corp',
      website_url: 'https://acme.com',
    }),
  });

  const data = await response.json();
  console.log('Agent created:', data.agent_id);
  console.log('WhatsApp:', data.whatsapp_link);
  console.log('Webchat:', data.webchat_embed_code);
};
```

### Python

```python
import requests

response = requests.post(
    'https://automanus.io/api/v1/public/agents',
    headers={
        'Authorization': 'Bearer ak_your_api_key',
        'Content-Type': 'application/json',
    },
    json={
        'company_name': 'Acme Corp',
        'website_url': 'https://acme.com',
    }
)

data = response.json()
print(f"Agent created: {data['agent_id']}")
print(f"WhatsApp: {data['whatsapp_link']}")
```

### React Webchat Integration

```jsx
import { useEffect } from 'react';

function AutoManusChat({ widgetKey }) {
  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://automanus.io/widget/automanus-chat.js';
    script.setAttribute('data-widget-key', widgetKey);
    document.body.appendChild(script);

    return () => document.body.removeChild(script);
  }, [widgetKey]);

  return null;
}

// Usage: <AutoManusChat widgetKey="wc_abc123" />
```

## Links

- [MCP Server Guide](../mcp/README.md) - For Claude Desktop, Cursor, Claude Code
- [Dashboard](https://automanus.io/dashboard) - Manage your agents
- [npm Package](https://www.npmjs.com/package/@automanus/mcp-server)

## Support

- **Email**: hello@automanus.io
- **GitHub Issues**: [Report a bug](https://github.com/automanus-io/docs/issues)
