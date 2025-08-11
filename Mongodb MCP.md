# MongoDB MCP Server

A Node.js TypeScript implementation of an MCP (Model Context Protocol) server for MongoDB database operations.

## Overview

This MCP server provides comprehensive access to MongoDB databases with support for multiple database connections. It offers 8 tools for complete database management including connections, queries, insertions, updates, and deletions.

### 🔧 **Available Tools**

1. **connect_database** - Connect to a specific MongoDB database
2. **find_documents** - Query documents with filtering, sorting, and pagination  
3. **insert_documents** - Insert one or more documents
4. **update_documents** - Update documents based on queries
5. **delete_documents** - Delete documents based on queries
6. **list_databases** - List all configured database connections
7. **get_collections** - Get all collections in connected database
8. **get_current_connection** - Get current connection information

### 🚀 **Key Features**

- 🔗 **Multiple Database Support**: Connect to multiple MongoDB databases via environment configuration
- 📊 **Full CRUD Operations**: Complete Create, Read, Update, Delete functionality
- 🔍 **Advanced Querying**: Support for filters, projections, sorting, limits, and skipping
- ⚡ **High Performance**: Native MongoDB Node.js driver
- 🛡️ **Error Handling**: Comprehensive error handling and validation
- 📝 **Detailed Responses**: JSON formatted responses with operation metadata
- 🆔 **ObjectId Support**: Proper MongoDB ObjectId serialization in `$oid` format

## Installation

### Install Dependencies

```bash
npm install
```

### Build Project

```bash
npm run build
```

## Configuration

Configure your MongoDB connections using environment variables:

```env
# Multiple MongoDB database connections
# Format: MONGODB_<DATABASE_NAME>_URI=mongodb://...
MONGODB_SIYA_ETL_URI=mongodb://localhost:27017/siya-etl
MONGODB_PRODUCTION_URI=mongodb://username:password@production-host:27017/production-db
MONGODB_ANALYTICS_URI=mongodb://localhost:27017/analytics

# Default limit for queries (optional, defaults to 10000)
DEFAULT_LIMIT=10000

# Debug mode (optional)
DEBUG=true
```

### Required Configuration

- **MONGODB_<NAME>_URI**: MongoDB connection strings for each database
- **DEFAULT_LIMIT**: Default limit for query results (optional, defaults to 10000)

## Usage

### Basic Usage

```bash
npm start
```

### Development Mode

```bash
npm run dev
```

### With Custom Configuration

Set environment variables before running:

```bash
export MONGODB_MAIN_URI="mongodb://localhost:27017/main-db"
export DEFAULT_LIMIT=5000
npm start
```

## Tool Examples

### Connect to Database

First, connect to one of your configured databases:

```json
{
  "name": "connect_database",
  "arguments": {
    "database_name": "siya-etl"
  }
}
```

Response:
```json
{
  "type": "text",
  "text": "Successfully connected to database: siya-etl",
  "title": "Database Connection"
}
```

### Find Documents

Query documents with optional filtering, sorting, and pagination:

```json
{
  "name": "find_documents",
  "arguments": {
    "collection": "users",
    "query": {"status": "active"},
    "projection": {"name": 1, "email": 1, "status": 1},
    "sort": {"createdAt": -1},
    "limit": 50,
    "skip": 0
  }
}
```

### Insert Documents

Insert single or multiple documents:

```json
{
  "name": "insert_documents",
  "arguments": {
    "collection": "users",
    "documents": [
      {
        "name": "John Doe",
        "email": "john@example.com",
        "status": "active"
      },
      {
        "name": "Jane Smith", 
        "email": "jane@example.com",
        "status": "active"
      }
    ]
  }
}
```

### Update Documents

Update documents based on query criteria:

```json
{
  "name": "update_documents",
  "arguments": {
    "collection": "users",
    "query": {"status": "pending"},
    "update": {"$set": {"status": "active", "updatedAt": "2024-01-01"}},
    "upsert": false,
    "multi": true
  }
}
```

### Delete Documents

Delete documents based on query criteria:

```json
{
  "name": "delete_documents",
  "arguments": {
    "collection": "users",
    "query": {"status": "inactive"},
    "delete_one": false
  }
}
```

### List Available Databases

```json
{
  "name": "list_databases"
}
```

### Get Collections

Get all collections in the currently connected database:

```json
{
  "name": "get_collections"
}
```

### Check Current Connection

```json
{
  "name": "get_current_connection"
}
```

## Database Connection Workflow

1. **Configure Environment**: Set up `MONGODB_<NAME>_URI` environment variables
2. **List Databases**: Use `list_databases` to see available connections
3. **Connect**: Use `connect_database` with the database name
4. **Explore**: Use `get_collections` to see available collections
5. **Query**: Use `find_documents`, `insert_documents`, etc. for operations

## Advanced Query Examples

### Complex Find Query

```json
{
  "name": "find_documents",
  "arguments": {
    "collection": "orders",
    "query": {
      "status": {"$in": ["pending", "processing"]},
      "createdAt": {"$gte": "2024-01-01"},
      "total": {"$gt": 100}
    },
    "projection": {
      "orderId": 1,
      "customer": 1,
      "total": 1,
      "status": 1,
      "createdAt": 1
    },
    "sort": {"createdAt": -1, "total": -1},
    "limit": 100
  }
}
```

### Aggregation-style Update

```json
{
  "name": "update_documents",
  "arguments": {
    "collection": "products",
    "query": {"category": "electronics"},
    "update": {
      "$mul": {"price": 0.9},
      "$set": {"onSale": true, "updatedAt": "2024-01-01"}
    },
    "multi": true
  }
}
```

### Conditional Delete

```json
{
  "name": "delete_documents",
  "arguments": {
    "collection": "logs",
    "query": {
      "level": "debug",
      "timestamp": {"$lt": "2024-01-01"}
    }
  }
}
```

## BSON ObjectId Handling

The server uses BSON (Binary JSON) for proper MongoDB ObjectId handling. It automatically converts `$oid` format to ObjectId instances for database operations and serializes ObjectId instances back to `$oid` format in responses.

### Inserting Documents with ObjectId References

```json
{
  "name": "insert_documents",
  "arguments": {
    "collection": "workspace_menus",
    "documents": [
      {
        "name": "Project Alpha",
        "workspace": [
          {
            "$oid": "507f1f77bcf86cd799439011"
          }
        ],
        "folders": [
          {
            "name": "Documents",
            "_id": {
              "$oid": "507f1f77bcf86cd799439012"
            },
            "files": [
              {
                "$oid": "507f1f77bcf86cd799439013"
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### Response with Proper ObjectId Format

The server returns ObjectId fields in the correct `$oid` format:

```json
{
  "collection": "workspace_menus",
  "operation": "insert",
  "insertedCount": 1,
  "insertedIds": {
    "0": "507f1f77bcf86cd799439014"
  },
  "insertedDocuments": [
    {
      "_id": {
        "$oid": "507f1f77bcf86cd799439014"
      },
      "name": "Project Alpha",
      "workspace": [
        {
          "$oid": "507f1f77bcf86cd799439011"
        }
      ],
      "folders": [
        {
          "name": "Documents",
          "_id": {
            "$oid": "507f1f77bcf86cd799439012"
          },
          "files": [
            {
              "$oid": "507f1f77bcf86cd799439013"
            }
          ]
        }
      ]
    }
  ],
  "success": true
}
```

### BSON Processing Features

- **Automatic Conversion**: `$oid` strings are automatically converted to ObjectId instances
- **Query Support**: ObjectId queries work with `$oid` format
- **Nested Processing**: Handles ObjectId fields in nested objects and arrays
- **Validation**: Validates ObjectId format before processing
- **Error Handling**: Provides clear error messages for invalid ObjectId formats

### Query Examples with ObjectId

```json
{
  "name": "find_documents",
  "arguments": {
    "collection": "workspace_menus",
    "query": {
      "workspace": {
        "$oid": "507f1f77bcf86cd799439011"
      },
      "folders._id": {
        "$oid": "507f1f77bcf86cd799439012"
      }
    }
  }
}
```

### Update Examples with ObjectId

```json
{
  "name": "update_documents",
  "arguments": {
    "collection": "workspace_menus",
    "query": {
      "_id": {
        "$oid": "507f1f77bcf86cd799439011"
      }
    },
    "update": {
      "$set": {
        "parent": {
          "$oid": "507f1f77bcf86cd799439012"
        }
      }
    }
  }
}
```

## Environment Configuration Examples

### Single Database

```env
MONGODB_MAIN_URI=mongodb://localhost:27017/myapp
DEFAULT_LIMIT=1000
```

### Multiple Databases

```env
MONGODB_USERS_URI=mongodb://localhost:27017/users
MONGODB_ORDERS_URI=mongodb://localhost:27017/orders  
MONGODB_ANALYTICS_URI=mongodb://analytics-host:27017/analytics
MONGODB_LOGS_URI=mongodb://logs-host:27017/logs
DEFAULT_LIMIT=5000
DEBUG=true
```

### Production Setup

```env
MONGODB_PRODUCTION_URI=mongodb+srv://user:pass@cluster.mongodb.net/production
MONGODB_STAGING_URI=mongodb+srv://user:pass@cluster.mongodb.net/staging
MONGODB_ANALYTICS_URI=mongodb+srv://user:pass@analytics.mongodb.net/analytics
DEFAULT_LIMIT=10000
```

## Error Handling

The server provides detailed error messages for common scenarios:

- **No Database Connection**: Prompts to connect first
- **Invalid Database Name**: Lists available configured databases
- **Query Errors**: MongoDB operation errors with details
- **Connection Failures**: Network and authentication errors

## Response Format

All tools return responses in this format:

```json
{
  "type": "text",
  "text": "JSON formatted operation results",
  "title": "Operation Description",
  "format": "json"
}
```

## Project Structure

```
mongodb-mcp-server/
├── src/
│   ├── index.ts          # Main server implementation
│   ├── tools.ts          # MongoDB tool implementations  
│   ├── tool-schema.ts    # Tool definitions and schemas
│   └── ...
├── dist/                 # Compiled output
├── bin/                  # CLI executable
├── package.json
├── .env.example          # Environment variable template
└── README.md
```

## Development

### Setup

```bash
git clone <repository>
cd mongodb-mcp-server
npm install
npm run build
```

### Testing

```bash
npm test
```

### Debugging

```bash
DEBUG=true npm run dev
```

## MongoDB Query Syntax

This server supports standard MongoDB query syntax:

### Comparison Operators
- `$eq`, `$ne`, `$gt`, `$gte`, `$lt`, `$lte`
- `$in`, `$nin`

### Logical Operators  
- `$and`, `$or`, `$not`, `$nor`

### Element Operators
- `$exists`, `$type`

### Array Operators
- `$all`, `$elemMatch`, `$size`

### Update Operators
- `$set`, `$unset`, `$inc`, `$mul`
- `$push`, `$pull`, `$addToSet`

## Security Considerations

- Store MongoDB connection strings securely
- Use authentication for production databases
- Implement network security (VPN, firewall rules)
- Regular backup and monitoring
- Use least privilege database users

## Support

For issues and support, please check:
- MongoDB Node.js driver documentation
- MongoDB query documentation  
- GitHub issues for this project

## License

MIT License - see LICENSE file for details.