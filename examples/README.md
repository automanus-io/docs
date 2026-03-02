# AutoManus Examples

Configuration examples and code snippets for integrating AutoManus.

## MCP Configuration Examples

### Claude Desktop (macOS)

**File:** `~/Library/Application Support/Claude/claude_desktop_config.json`

#### Basic Setup

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

#### With Pre-configured Email

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

#### With API Key (Pro Users)

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

### Claude Desktop (Windows)

**File:** `%APPDATA%\Claude\claude_desktop_config.json`

Same JSON structure as macOS above.

### Cursor

**File:** `~/.cursor/mcp.json`

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

**File:** `~/.claude/mcp.json`

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

---

## Webchat Widget Examples

### Basic HTML

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Website</title>
</head>
<body>
  <h1>Welcome to My Website</h1>

  <!-- AutoManus Webchat Widget -->
  <script
    src="https://automanus.io/widget/automanus-chat.js"
    data-widget-key="wc_your_widget_key">
  </script>
</body>
</html>
```

### React

```jsx
import { useEffect } from 'react';

function AutoManusChat({ widgetKey }) {
  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://automanus.io/widget/automanus-chat.js';
    script.setAttribute('data-widget-key', widgetKey);
    document.body.appendChild(script);

    return () => {
      document.body.removeChild(script);
    };
  }, [widgetKey]);

  return null;
}

// Usage
function App() {
  return (
    <div>
      <h1>My App</h1>
      <AutoManusChat widgetKey="wc_your_widget_key" />
    </div>
  );
}
```

### Next.js

```jsx
// components/AutoManusChat.tsx
'use client';

import { useEffect } from 'react';

interface Props {
  widgetKey: string;
}

export default function AutoManusChat({ widgetKey }: Props) {
  useEffect(() => {
    const script = document.createElement('script');
    script.src = 'https://automanus.io/widget/automanus-chat.js';
    script.setAttribute('data-widget-key', widgetKey);
    script.async = true;
    document.body.appendChild(script);

    return () => {
      document.body.removeChild(script);
    };
  }, [widgetKey]);

  return null;
}
```

```jsx
// app/layout.tsx or page.tsx
import AutoManusChat from '@/components/AutoManusChat';

export default function Layout({ children }) {
  return (
    <html>
      <body>
        {children}
        <AutoManusChat widgetKey="wc_your_widget_key" />
      </body>
    </html>
  );
}
```

### Vue.js

```vue
<template>
  <div>
    <h1>My App</h1>
  </div>
</template>

<script>
export default {
  name: 'App',
  mounted() {
    const script = document.createElement('script');
    script.src = 'https://automanus.io/widget/automanus-chat.js';
    script.setAttribute('data-widget-key', 'wc_your_widget_key');
    document.body.appendChild(script);
  },
  beforeUnmount() {
    const script = document.querySelector('script[data-widget-key]');
    if (script) {
      document.body.removeChild(script);
    }
  }
};
</script>
```

---

## API Integration Examples

### Node.js - Create Agent and Add Knowledge

```javascript
const AUTOMANUS_API_KEY = process.env.AUTOMANUS_API_KEY;
const BASE_URL = 'https://automanus.io/api/v1/public';

async function createAgentWithKnowledge() {
  // Step 1: Create the agent
  const agentResponse = await fetch(`${BASE_URL}/agents`, {
    method: 'POST',
    headers: {
      'Authorization': `Bearer ${AUTOMANUS_API_KEY}`,
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({
      company_name: 'Acme Corp',
      website_url: 'https://acme.com',
    }),
  });

  const agent = await agentResponse.json();
  console.log('Agent created:', agent.agent_id);

  // Step 2: Add custom knowledge
  const faqs = [
    { title: 'Pricing', content: 'Our Pro plan is $99/month' },
    { title: 'Refunds', content: 'Full refund within 30 days' },
    { title: 'Support', content: 'Email support@acme.com' },
  ];

  for (const faq of faqs) {
    await fetch(`${BASE_URL}/knowledge`, {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${AUTOMANUS_API_KEY}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        agent_id: agent.agent_id,
        title: faq.title,
        content: faq.content,
        item_type: 'faq',
      }),
    });
  }

  console.log('Knowledge added!');
  return agent;
}
```

### Python - Batch Knowledge Import

```python
import requests
import csv

API_KEY = 'ak_your_api_key'
BASE_URL = 'https://automanus.io/api/v1/public'

def import_knowledge_from_csv(agent_id, csv_file):
    """Import FAQs from a CSV file with columns: title, content, category"""

    headers = {
        'Authorization': f'Bearer {API_KEY}',
        'Content-Type': 'application/json',
    }

    with open(csv_file, 'r') as f:
        reader = csv.DictReader(f)
        for row in reader:
            response = requests.post(
                f'{BASE_URL}/knowledge',
                headers=headers,
                json={
                    'agent_id': agent_id,
                    'title': row['title'],
                    'content': row['content'],
                    'category': row.get('category', 'General'),
                    'item_type': 'faq',
                }
            )

            if response.ok:
                print(f"Added: {row['title']}")
            else:
                print(f"Failed: {row['title']} - {response.json()}")

# Usage
import_knowledge_from_csv('agent-uuid-here', 'faqs.csv')
```

### cURL - Complete Workflow

```bash
#!/bin/bash

API_KEY="ak_your_api_key"
BASE_URL="https://automanus.io/api/v1/public"

# 1. Create agent
AGENT_RESPONSE=$(curl -s -X POST "$BASE_URL/agents" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "company_name": "Acme Corp",
    "website_url": "https://acme.com"
  }')

AGENT_ID=$(echo $AGENT_RESPONSE | jq -r '.agent_id')
echo "Created agent: $AGENT_ID"

# 2. Add knowledge
curl -X POST "$BASE_URL/knowledge" \
  -H "Authorization: Bearer $API_KEY" \
  -H "Content-Type: application/json" \
  -d "{
    \"agent_id\": \"$AGENT_ID\",
    \"title\": \"Pricing FAQ\",
    \"content\": \"Our Pro plan is \$99/month with unlimited users.\",
    \"item_type\": \"faq\"
  }"

echo "Knowledge added!"

# 3. Get status
curl -s "$BASE_URL/agents/$AGENT_ID/status" \
  -H "Authorization: Bearer $API_KEY" | jq
```

---

## Natural Language Prompts

### Creating Agents

```
"Create an AI sales agent for Stripe"

"Build a customer service bot for my SaaS company TechFlow at techflow.io"

"I need a sales chatbot for my cleaning business SparkClean, website sparkclean.com"

"Set up an AI agent for Acme Corp, my email is john@acme.com"
```

### Adding Knowledge

```
"Add a FAQ to the Acme agent: Our pricing starts at $49/month"

"Add product info: The Pro plan includes unlimited users, priority support, and API access"

"Add this policy to agent abc-123: Refunds are available within 30 days of purchase"
```

### Checking Status

```
"What's the status of my Acme agent?"

"How many knowledge items does agent abc-123 have?"

"Is the TechFlow agent deployed to WhatsApp?"
```

---

## Links

- [MCP Server Guide](../mcp/README.md)
- [API Reference](../api/README.md)
- [Dashboard](https://automanus.io/dashboard)
