> Forked from [dpflucas/mysql-mcp-server](https://github.com/dpflucas/mysql-mcp-server)

# mysql-mcp-stdio

A MySQL MCP (Model Context Protocol) server using stdio transport. Provides read-only access to MySQL databases.

## Features

- List available databases
- List tables in a database
- Describe table schemas
- Execute read-only SQL queries

## Security

- **Read-only access**: Only SELECT, SHOW, DESCRIBE, and EXPLAIN statements are allowed
- **Query validation**: Prevents SQL injection and blocks data modification attempts
- **Query timeout**: Prevents long-running queries from consuming resources
- **Row limit**: Prevents excessive data return

## Installation

```bash
# Clone the repository
git clone git@github.com:chenfukun/mysql-mcp-stdio.git
cd mysql-mcp-stdio

# Install dependencies and build
npm install
npm run build
```

## Configuration

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `MYSQL_HOST` | Database server hostname | `localhost` |
| `MYSQL_PORT` | Database server port | `3306` |
| `MYSQL_USER` | Database username | - |
| `MYSQL_PASSWORD` | Database password | - |
| `MYSQL_DATABASE` | Default database name | - |
| `MYSQL_CONNECTION_LIMIT` | Max connections in pool | `10` |
| `MYSQL_QUEUE_LIMIT` | Max queued connection requests | `0` (unlimited) |
| `MYSQL_CONNECT_TIMEOUT` | Connection timeout (ms) | `10000` |
| `MYSQL_IDLE_TIMEOUT` | Idle connection timeout (ms) | `60000` |
| `MYSQL_MAX_IDLE` | Max idle connections in pool | `10` |

### MCP Settings Example

```json
{
  "mcpServers": {
    "mysql": {
      "command": "node",
      "args": ["/path/to/mysql-mcp-stdio/build/index.js"],
      "env": {
        "MYSQL_HOST": "localhost",
        "MYSQL_PORT": "3306",
        "MYSQL_USER": "your-mysql-user",
        "MYSQL_PASSWORD": "your-mysql-password",
        "MYSQL_DATABASE": "your-default-database"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

## Timezone

Date/time values are returned as raw strings from the database (`dateStrings: true`), without any conversion.

- **DATETIME columns**: Returned as-is (e.g. `"2026-05-10 11:24:00"`)
- **TIMESTAMP columns**: Displayed according to the session timezone
- **NOW()**: Returns the current time in the session timezone

By default, the session timezone follows the server's global setting. If you need a specific timezone for a query, use the `timezone` parameter in `execute_query`. You can also check the server timezone with:

```sql
SELECT @@global.time_zone, @@session.time_zone
```

## Available Tools

### list_databases

Lists all accessible databases on the MySQL server.

### list_tables

Lists all tables in a specified database.

**Parameters**:
- `database` (optional): Database name (uses default if not specified)

### describe_table

Shows the schema for a specific table.

**Parameters**:
- `database` (optional): Database name
- `table` (required): Table name

### execute_query

Executes a read-only SQL query.

**Parameters**:
- `query` (required): SQL query (only SELECT, SHOW, DESCRIBE, and EXPLAIN)
- `database` (optional): Database name
- `timezone` (optional): Session timezone for this query (e.g. `+08:00`, `+00:00`). Affects TIMESTAMP display and NOW(). Does not affect DATETIME columns.

## Acknowledgements

This project is based on [mysql-mcp-server](https://github.com/dpflucas/mysql-mcp-server) by Diogo Lucas. Thanks for the original work.

## License

MIT
