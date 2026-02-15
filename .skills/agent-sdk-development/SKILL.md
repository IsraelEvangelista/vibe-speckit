---
name: agent-sdk-development
description: Build SDKs and APIs designed for AI agents to consume, with proper tool schemas, MCP integration, and type-safe interfaces.
triggers:
  - keywords: ["agent sdk", "mcp", "model context protocol", "ai sdk", "agent api", "tool schema", "function calling", "llm tools"]
  - file_patterns: ["**/mcp/**", "**/agent-sdk/**", "**/tools/**", "**/*agent*"]
  - code_patterns: ["mcp.server", "ToolSchema", "function calling", "z.object"]
---

# Agent SDK Development

Build SDKs and APIs optimized for AI agent consumption, with focus on discoverability, type safety, and MCP (Model Context Protocol) compliance.

## Overview

Agent SDKs differ from traditional APIs: they expose "tools" that LLMs can discover and invoke, with rich schemas for parameter validation and clear descriptions.

## Key Principles

1. **Tool-centric design** - Everything is a callable tool
2. **Rich metadata** - Descriptions matter for LLM understanding
3. **Type safety** - Strong typing for all parameters
4. **MCP compliance** - Follow Model Context Protocol standards
5. **Observability** - Log and trace agent interactions

## Architecture Patterns

### Tool Definition with Zod

```typescript
// sdk/src/tools/search.ts
import { z } from 'zod';
import { defineTool } from '../core/tool';

const SearchSchema = z.object({
  query: z.string()
    .min(1)
    .max(200)
    .describe('The search query string. Be specific and use keywords.'),
  limit: z.number()
    .int()
    .min(1)
    .max(50)
    .default(10)
    .describe('Maximum number of results to return.'),
  filters: z.object({
    category: z.enum(['all', 'tech', 'business', 'science'])
      .default('all')
      .describe('Filter results by category.'),
    dateFrom: z.string()
      .datetime()
      .optional()
      .describe('ISO 8601 date string for filtering results from this date.')
  }).optional()
});

export const searchTool = defineTool({
  name: 'search_documents',
  description: `
    Search through the document database.
    Use this when the user asks about specific information that might be in documents.
    Returns matching documents with relevance scores.
  `,
  parameters: SearchSchema,
  
  async execute(params) {
    const { query, limit, filters } = params;
    
    // Implementation
    const results = await documentRepository.search({
      query,
      limit,
      filters
    });
    
    return {
      success: true,
      count: results.length,
      results: results.map(doc => ({
        id: doc.id,
        title: doc.title,
        excerpt: doc.excerpt.substring(0, 200),
        relevance: doc.score,
        url: doc.url
      }))
    };
  }
});
```

### Tool Registry

```typescript
// sdk/src/core/registry.ts
import { Tool } from './types';

export class ToolRegistry {
  private tools = new Map<string, Tool<any, any>>();
  
  register<TParams, TResult>(tool: Tool<TParams, TResult>): void {
    if (this.tools.has(tool.name)) {
      throw new Error(`Tool '${tool.name}' already registered`);
    }
    this.tools.set(tool.name, tool);
  }
  
  get(name: string): Tool | undefined {
    return this.tools.get(name);
  }
  
  list(): Tool[] {
    return Array.from(this.tools.values());
  }
  
  async execute(name: string, params: unknown): Promise<unknown> {
    const tool = this.get(name);
    if (!tool) {
      throw new Error(`Tool '${name}' not found`);
    }
    
    // Validate params
    const validated = tool.parameters.parse(params);
    
    // Execute
    return tool.execute(validated);
  }
}

// Singleton instance
export const registry = new ToolRegistry();
```

### SDK Client

```typescript
// sdk/src/client.ts
import { ToolRegistry } from './core/registry';
import { Tool } from './core/types';

export class AgentSDK {
  private registry: ToolRegistry;
  private baseUrl: string;
  private apiKey?: string;
  
  constructor(config: { baseUrl: string; apiKey?: string }) {
    this.baseUrl = config.baseUrl;
    this.apiKey = config.apiKey;
    this.registry = new ToolRegistry();
  }
  
  // Register local tools
  registerTool(tool: Tool): void {
    this.registry.register(tool);
  }
  
  // Get tool definitions for LLM
  getToolDefinitions(): Array<{
    name: string;
    description: string;
    parameters: unknown;
  }> {
    return this.registry.list().map(tool => ({
      name: tool.name,
      description: tool.description,
      parameters: zodToJsonSchema(tool.parameters)
    }));
  }
  
  // Execute a tool by name
  async execute(name: string, params: unknown): Promise<unknown> {
    return this.registry.execute(name, params);
  }
  
  // Call remote tool via API
  async callRemote(toolName: string, params: unknown): Promise<unknown> {
    const response = await fetch(`${this.baseUrl}/tools/${toolName}`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        ...(this.apiKey && { 'Authorization': `Bearer ${this.apiKey}` })
      },
      body: JSON.stringify(params)
    });
    
    if (!response.ok) {
      throw new Error(`Tool execution failed: ${response.statusText}`);
    }
    
    return response.json();
  }
}
```

## MCP (Model Context Protocol) Integration

### MCP Server Setup

```typescript
// sdk/src/mcp/server.ts
import { Server } from '@modelcontextprotocol/sdk/server/index.js';
import { StdioServerTransport } from '@modelcontextprotocol/sdk/server/stdio.js';
import {
  CallToolRequestSchema,
  ListToolsRequestSchema,
} from '@modelcontextprotocol/sdk/types.js';
import { ToolRegistry } from '../core/registry';

export function createMCPServer(registry: ToolRegistry) {
  const server = new Server(
    {
      name: 'my-agent-sdk',
      version: '1.0.0',
    },
    {
      capabilities: {
        tools: {},
      },
    }
  );
  
  // List available tools
  server.setRequestHandler(ListToolsRequestSchema, async () => {
    const tools = registry.list().map(tool => ({
      name: tool.name,
      description: tool.description,
      inputSchema: zodToJsonSchema(tool.parameters)
    }));
    
    return { tools };
  });
  
  // Execute a tool
  server.setRequestHandler(CallToolRequestSchema, async (request) => {
    const { name, arguments: args } = request.params;
    
    try {
      const result = await registry.execute(name, args);
      
      return {
        content: [
          {
            type: 'text',
            text: JSON.stringify(result, null, 2)
          }
        ]
      };
    } catch (error) {
      return {
        content: [
          {
            type: 'text',
            text: `Error: ${error.message}`
          }
        ],
        isError: true
      };
    }
  });
  
  return server;
}

// Start the server
export async function startServer(registry: ToolRegistry) {
  const server = createMCPServer(registry);
  const transport = new StdioServerTransport();
  
  await server.connect(transport);
  console.error('MCP Server running on stdio');
}
```

### MCP Client

```typescript
// sdk/src/mcp/client.ts
import { Client } from '@modelcontextprotocol/sdk/client/index.js';
import { StdioClientTransport } from '@modelcontextprotocol/sdk/client/stdio.js';

export async function createMCPClient(command: string, args: string[] = []) {
  const transport = new StdioClientTransport({
    command,
    args
  });
  
  const client = new Client(
    {
      name: 'my-client',
      version: '1.0.0'
    },
    {
      capabilities: {
        tools: {}
      }
    }
  );
  
  await client.connect(transport);
  
  return {
    async listTools() {
      return client.listTools();
    },
    
    async callTool(name: string, args: unknown) {
      return client.callTool({
        name,
        arguments: args as Record<string, unknown>
      });
    },
    
    async close() {
      await transport.close();
    }
  };
}
```

## Tool Design Best Practices

### Granularity

```typescript
// ❌ Too broad - hard for LLM to use correctly
const badTool = defineTool({
  name: 'do_everything',
  description: 'Does various things',
  parameters: z.object({
    action: z.enum(['create', 'update', 'delete', 'list', 'search']),
    data: z.any()
  })
});

// ✅ Specific - clear intent, easier to use
const goodTools = [
  defineTool({
    name: 'create_user',
    description: 'Create a new user with email and name',
    parameters: z.object({
      email: z.string().email(),
      name: z.string().min(1)
    })
  }),
  
  defineTool({
    name: 'list_users',
    description: 'List all users with optional pagination',
    parameters: z.object({
      page: z.number().default(1),
      limit: z.number().max(100).default(20)
    })
  })
];
```

### Error Handling

```typescript
const robustTool = defineTool({
  name: 'fetch_data',
  description: 'Fetch data from external API',
  parameters: z.object({
    url: z.string().url()
  }),
  
  async execute({ url }) {
    try {
      const response = await fetch(url, {
        timeout: 10000
      });
      
      if (!response.ok) {
        return {
          success: false,
          error: `HTTP ${response.status}: ${response.statusText}`,
          retryable: response.status >= 500
        };
      }
      
      const data = await response.json();
      
      return {
        success: true,
        data,
        cached: false
      };
    } catch (error) {
      if (error.name === 'TimeoutError') {
        return {
          success: false,
          error: 'Request timeout',
          retryable: true
        };
      }
      
      return {
        success: false,
        error: error.message,
        retryable: false
      };
    }
  }
});
```

### Context Preservation

```typescript
const contextualTool = defineTool({
  name: 'continue_conversation',
  description: 'Continue a conversation with context from previous messages',
  parameters: z.object({
    message: z.string(),
    conversationId: z.string()
      .describe('ID from previous turn, or omit to start new conversation')
      .optional()
  }),
  
  async execute({ message, conversationId }) {
    // Load previous context
    const context = conversationId 
      ? await contextStore.load(conversationId)
      : await contextStore.create();
    
    // Process with context
    const result = await processMessage(message, context);
    
    // Save updated context
    await contextStore.save(context.id, result.context);
    
    return {
      response: result.text,
      conversationId: context.id,
      suggestions: result.followUpQuestions
    };
  }
});
```

## Authentication & Security

```typescript
// sdk/src/middleware/auth.ts
import { Request, Response, NextFunction } from 'express';

export function authenticateAgent(
  req: Request,
  res: Response,
  next: NextFunction
) {
  const authHeader = req.headers.authorization;
  
  if (!authHeader?.startsWith('Bearer ')) {
    return res.status(401).json({
      error: 'Missing or invalid authorization header'
    });
  }
  
  const token = authHeader.slice(7);
  
  try {
    const payload = verifyToken(token);
    req.agent = {
      id: payload.sub,
      permissions: payload.permissions,
      rateLimit: payload.rateLimit
    };
    next();
  } catch (error) {
    return res.status(401).json({
      error: 'Invalid token'
    });
  }
}
```

## Observability

```typescript
// sdk/src/middleware/observability.ts
import { performance } from 'perf_hooks';

export function traceToolExecution(
  registry: ToolRegistry
): ToolRegistry {
  const originalExecute = registry.execute.bind(registry);
  
  registry.execute = async (name: string, params: unknown) => {
    const start = performance.now();
    
    console.log(`[TOOL] Executing: ${name}`, {
      params,
      timestamp: new Date().toISOString()
    });
    
    try {
      const result = await originalExecute(name, params);
      
      const duration = performance.now() - start;
      console.log(`[TOOL] Success: ${name}`, {
        duration: `${duration.toFixed(2)}ms`,
        resultType: typeof result
      });
      
      return result;
    } catch (error) {
      const duration = performance.now() - start;
      console.error(`[TOOL] Error: ${name}`, {
        duration: `${duration.toFixed(2)}ms`,
        error: error.message
      });
      
      throw error;
    }
  };
  
  return registry;
}
```

## Testing Agent Tools

```typescript
// sdk/src/__tests__/tools.test.ts
import { describe, it, expect, vi } from 'vitest';
import { searchTool } from '../tools/search';

describe('searchTool', () => {
  it('should validate parameters', async () => {
    await expect(
      searchTool.execute({ query: '' })
    ).rejects.toThrow();
  });
  
  it('should return search results', async () => {
    const result = await searchTool.execute({
      query: 'test query',
      limit: 5
    });
    
    expect(result.success).toBe(true);
    expect(result.results).toHaveLength(<= 5);
  });
  
  it('should apply filters', async () => {
    const result = await searchTool.execute({
      query: 'test',
      filters: {
        category: 'tech'
      }
    });
    
    expect(result.results.every(r => 
      r.category === 'tech'
    )).toBe(true);
  });
});
```

## Documentation for LLMs

```markdown
## Tool: search_documents

Search the knowledge base for relevant documents.

### When to use
- User asks about specific information
- Need to find references or sources
- Looking for previous work or decisions

### Parameters
- `query` (string, required): The search terms. Be specific.
- `limit` (number, optional): Max results (1-50, default: 10)
- `filters.category` (enum): Filter by category

### Returns
- `results`: Array of matching documents with relevance scores
- `count`: Total number of results found

### Example
```json
{
  "query": "authentication implementation",
  "limit": 5,
  "filters": { "category": "tech" }
}
```
```

## Recommended Libraries

| Purpose | Libraries |
|---------|-----------|
| Schema validation | `zod` |
| MCP SDK | `@modelcontextprotocol/sdk` |
| JSON Schema | `zod-to-json-schema` |
| Testing | `vitest` |
| HTTP client | `fetch` or `axios` |
| Authentication | `jsonwebtoken` |

## Key Takeaways

1. Design tools for LLM comprehension, not just API completeness
2. Use rich descriptions and examples
3. Validate strictly with Zod
4. Follow MCP protocol for interoperability
5. Implement proper error handling and retry logic
6. Add observability for debugging
7. Test with realistic LLM prompts
8. Document usage patterns clearly
