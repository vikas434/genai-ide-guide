# PostgreSQL MCP Integration Guide

After working extensively with PostgreSQL in Cursor through MCP, I've found it to be incredibly useful for database operations. Here's my practical guide on setting it up and using it effectively.

## Setting Up PostgreSQL MCP

The setup is straightforward but requires careful handling of credentials. Here's how I did it:

1. First, download the GenAI toolbox that includes the PostgreSQL MCP server
2. Place the toolbox in your project directory
3. Configure your MCP settings in `.cursor/mcp.json`

Here's a basic configuration template:
```json
{
  "mcpServers": {
    "postgres": {
      "command": "./toolbox",
      "args": ["--prebuilt", "postgres", "--stdio"],
      "env": {
        "POSTGRES_HOST": "your-host",
        "POSTGRES_PORT": "your-port",
        "POSTGRES_DATABASE": "your-database",
        "POSTGRES_USER": "your-username",
        "POSTGRES_PASSWORD": "your-password"
      }
    }
  }
}
```

## Daily Usage

I use PostgreSQL MCP for various tasks:

1. Quick Data Exploration
   - Run SELECT queries directly from the editor
   - Check table structures
   - Verify data integrity

2. Database Management
   - Create and modify tables
   - Manage indexes
   - Monitor database performance

3. Development Tasks
   - Test queries before adding to code
   - Debug database issues
   - Quick data validation

## Security Tips

From my experience, here are crucial security practices:

1. Credential Management
   - Never commit database credentials to version control
   - Use environment variables when possible
   - Consider using connection pooling for production

2. Access Control
   - Use read-only accounts for development
   - Create separate users for different environments
   - Regularly rotate passwords

3. Connection Security
   - Use SSL for remote connections
   - Limit database access to specific IP ranges
   - Keep your toolbox version updated

## Troubleshooting Common Issues

Problems I've encountered and their solutions:

1. Connection Issues
   - Check if PostgreSQL is running
   - Verify port accessibility
   - Confirm network/firewall settings

2. Authentication Problems
   - Double-check credentials
   - Verify user permissions
   - Check database name

3. Performance
   - Monitor query execution time
   - Use EXPLAIN ANALYZE for optimization
   - Consider connection pooling for multiple queries

## Best Practices

Based on my daily usage:

1. Query Management
   - Start with SELECT queries to verify data
   - Use transactions for multiple operations
   - Keep queries focused and optimized

2. Development Workflow
   - Test queries in stages
   - Document complex queries
   - Use comments for clarity

3. Team Collaboration
   - Share query templates
   - Document database changes
   - Maintain consistent naming conventions

## Useful Commands

Here are some commands I frequently use:

1. Basic Operations
```sql
-- Check table structure
SELECT column_name, data_type 
FROM information_schema.columns 
WHERE table_name = 'your_table';

-- Quick row count
SELECT COUNT(*) FROM your_table;

-- Basic data exploration
SELECT * FROM your_table LIMIT 5;
```

2. Maintenance
```sql
-- Index usage
SELECT schemaname, tablename, indexname, idx_scan 
FROM pg_stat_user_indexes;

-- Table sizes
SELECT pg_size_pretty(pg_total_relation_size('your_table'));
```

Remember to always test queries with LIMIT first, especially on large tables!