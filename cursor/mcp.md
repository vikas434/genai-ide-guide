# Model Context Protocol (MCP)

The Model Context Protocol (MCP) is an open protocol that standardizes how applications provide context to Large Language Models (LLMs). Think of MCP like a USB-C port for AI applications - it provides a standardized way to connect AI models to different data sources and tools.

## Theory and Architecture

### Core Concepts

1. **Protocol Components**
   - **Hosts**: LLM applications (like Cursor) that initiate connections
   - **Clients**: Connectors within the host application that maintain connections
   - **Servers**: Services that provide context and capabilities
   - **Tools**: Functions exposed by servers for LLMs to use

2. **Communication Flow**
   ```mermaid
   graph LR
      A[Host/LLM] --> B[MCP Client]
      B --> C[MCP Server]
      C --> D[External Service]
      D --> C
      C --> B
      B --> A
   ```

3. **Protocol Features**
   - **Resources**: Context and data access
   - **Prompts**: Templated messages and workflows
   - **Tools**: Executable functions
   - **Sampling**: Server-initiated LLM interactions

### Architecture Components

1. **Transport Layer**
   - JSON-RPC 2.0 message format
   - Bidirectional communication
   - Support for streaming responses
   - Error handling and status codes

2. **Security Layer**
   - Authentication and authorization
   - Token management
   - Access control
   - Data encryption

3. **Tool Management**
   - Tool registration
   - Parameter validation
   - Execution context
   - Result handling

4. **State Management**
   - Session handling
   - Context persistence
   - Cache management
   - Error recovery

## Server Examples

### 1. GitHub MCP Server Example

Here's a basic implementation of a GitHub MCP server:

```typescript
import { MCPServer, Tool, ToolResult } from '@mcp/core';
import { Octokit } from '@octokit/rest';

class GitHubMCPServer extends MCPServer {
  private octokit: Octokit;

  constructor(token: string) {
    super();
    this.octokit = new Octokit({ auth: token });
    this.registerTools();
  }

  private registerTools() {
    // Create Repository Tool
    this.registerTool({
      name: 'create_repository',
      description: 'Create a new GitHub repository',
      parameters: {
        name: { type: 'string', required: true },
        description: { type: 'string', required: false },
        private: { type: 'boolean', default: false }
      },
      handler: async (params) => {
        try {
          const result = await this.octokit.repos.createForAuthenticatedUser({
            name: params.name,
            description: params.description,
            private: params.private
          });
          return new ToolResult(true, result.data);
        } catch (error) {
          return new ToolResult(false, null, error.message);
        }
      }
    });

    // Search Code Tool
    this.registerTool({
      name: 'search_code',
      description: 'Search for code in repositories',
      parameters: {
        query: { type: 'string', required: true },
        language: { type: 'string', required: false }
      },
      handler: async (params) => {
        try {
          const query = params.language 
            ? `${params.query} language:${params.language}`
            : params.query;
          
          const result = await this.octokit.search.code({
            q: query
          });
          return new ToolResult(true, result.data);
        } catch (error) {
          return new ToolResult(false, null, error.message);
        }
      }
    });
  }
}

// Usage
const server = new GitHubMCPServer(process.env.GITHUB_TOKEN);
server.start();
```

### 2. PostgreSQL MCP Server Example

Here's a basic implementation of a PostgreSQL MCP server:

```typescript
import { MCPServer, Tool, ToolResult } from '@mcp/core';
import { Pool } from 'pg';

class PostgreSQLMCPServer extends MCPServer {
  private pool: Pool;

  constructor(connectionString: string) {
    super();
    this.pool = new Pool({ connectionString });
    this.registerTools();
  }

  private registerTools() {
    // Execute Query Tool
    this.registerTool({
      name: 'execute_query',
      description: 'Execute a SQL query',
      parameters: {
        sql: { type: 'string', required: true },
        params: { type: 'array', required: false }
      },
      handler: async (params) => {
        try {
          const result = await this.pool.query(params.sql, params.params);
          return new ToolResult(true, {
            rows: result.rows,
            rowCount: result.rowCount
          });
        } catch (error) {
          return new ToolResult(false, null, error.message);
        }
      }
    });

    // List Tables Tool
    this.registerTool({
      name: 'list_tables',
      description: 'List all tables in the database',
      parameters: {
        schema: { type: 'string', default: 'public' }
      },
      handler: async (params) => {
        try {
          const sql = `
            SELECT table_name, column_name, data_type
            FROM information_schema.columns
            WHERE table_schema = $1
            ORDER BY table_name, ordinal_position;
          `;
          const result = await this.pool.query(sql, [params.schema]);
          return new ToolResult(true, result.rows);
        } catch (error) {
          return new ToolResult(false, null, error.message);
        }
      }
    });
  }

  async start() {
    try {
      await this.pool.connect();
      console.log('Connected to PostgreSQL');
      super.start();
    } catch (error) {
      console.error('Failed to connect to PostgreSQL:', error);
      process.exit(1);
    }
  }

  async stop() {
    await this.pool.end();
    super.stop();
  }
}

// Usage
const server = new PostgreSQLMCPServer(process.env.DATABASE_URL);
server.start();
```

## Integration Methods

[Previous integration methods content remains the same...]

## Security Best Practices

[Previous security content remains the same...]

## Troubleshooting

[Previous troubleshooting content remains the same...]

## Resources

- [Official MCP Documentation](https://modelcontextprotocol.io)
- [MCP GitHub Repository](https://github.com/modelcontextprotocol)
- [Integration Examples](https://modelcontextprotocol.io/examples)
- [Security Guidelines](https://modelcontextprotocol.io/security)