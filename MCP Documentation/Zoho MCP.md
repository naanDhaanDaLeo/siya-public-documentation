# MMPL Zoho Creator MCP Server

A Node.js TypeScript implementation of an MCP (Model Context Protocol) server for Zoho Creator API integration.

## Overview

This MCP server provides comprehensive access to Zoho Creator API v2 endpoints. It offers 25 tools organized into six categories for complete application and data management:

### 🔧 **Tool Categories**

#### Authentication (1 tool)
- **OAuth Authentication**: Complete OAuth2 flow to obtain access and refresh tokens

#### Meta APIs (7 tools)
- **Application Management**: Get applications, workspace applications, sections, forms, reports, pages
- **Field Management**: Retrieve form field definitions

#### Data APIs (8 tools)
- **Record Operations**: Quick view, detail view, add, update, delete records
- **Bulk Operations**: Support for batch processing

#### File APIs (3 tools)
- **File Management**: Upload, download files from records and subforms

#### Bulk APIs (3 tools)
- **Bulk Processing**: Create bulk read jobs, check status, download results

#### Publish APIs (3 tools)
- **Public Access**: Public API endpoints for forms and reports

### 🚀 **Key Features**

- 🔗 **Complete API Coverage**: All 24 Zoho Creator API v2 endpoints
- 🌍 **Multi-Data Center**: Support for all Zoho data centers (US, IN, EU, JP, SA, CA, AU, CN)
- 🔐 **OAuth2 Authentication**: Secure bearer token authentication
- 📊 **Structured Responses**: JSON formatted responses with proper error handling
- ⚡ **High Performance**: Native Node.js HTTP requests

## Installation

### Global Installation

```bash
npm install -g zoho-creator-mcp-server
```

### Local Installation

```bash
npm install zoho-creator-mcp-server
```

Or run directly with npx:

```bash
npx zoho-creator-mcp-server
```

## Authentication

### OAuth2 Authentication

The server includes a built-in OAuth2 authentication tool that handles the complete authentication flow:

#### `zoho_oauth_authenticate`

This tool performs the complete OAuth2 flow to obtain access and refresh tokens from Zoho.

**Parameters:**
- `redirect_uri` (string, optional): OAuth redirect URI (default: http://127.0.0.1:5000/oauth/callback)
- `scopes` (string, optional): OAuth scopes (default: ZohoCreator.form.CREATE,ZohoCreator.report.READ,ZohoCreator.meta.READ)
- `port` (integer, optional): Port for OAuth callback server (default: 5000)

**Required Environment Variables:**
- `CLIENT_ID`: Your Zoho OAuth Client ID
- `CLIENT_SECRET`: Your Zoho OAuth Client Secret

**Example:**
```json
{
  "name": "zoho_oauth_authenticate",
  "arguments": {
    "redirect_uri": "http://127.0.0.1:5000/oauth/callback",
    "scopes": "ZohoCreator.form.CREATE,ZohoCreator.report.READ,ZohoCreator.meta.READ",
    "port": 5000
  }
}
```

**Response:**
```json
{
  "success": true,
  "access_token": "1000.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "refresh_token": "1000.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "expires_in": 3600,
  "token_type": "Bearer",
  "api_domain": "https://www.zohoapis.com",
  "expires_at": 1234567890
}
```

**How it works:**
1. Starts a local HTTP server on the specified port
2. Opens your browser to Zoho's authorization page
3. Handles the OAuth callback automatically
4. Exchanges the authorization code for tokens
5. Returns the tokens in JSON format

## Configuration

```env
# Zoho Creator API Configuration
ZOHO_ACCESS_TOKEN=your-zoho-access-token
ZOHO_DATA_CENTER=US

# OAuth Configuration (required for zoho_oauth_authenticate tool)
CLIENT_ID=your-zoho-client-id
CLIENT_SECRET=your-zoho-client-secret
```

### Required Configuration

- **ZOHO_ACCESS_TOKEN**: Your Zoho OAuth2 access token
- **ZOHO_DATA_CENTER**: Data center (US, IN, EU, JP, SA, CA, AU, CN)

## Usage

### Basic Usage

```bash
npx zoho-creator-mcp-server
```

### With Custom Configuration

```bash
npx zoho-creator-mcp-server --zoho-access-token your-token --zoho-data-center EU
```

### Development Mode

```bash
cd zoho-creator-mcp-server
npm run dev
```

## Command Line Options

- `--zoho-access-token TOKEN` - Zoho OAuth2 access token
- `--zoho-data-center DC` - Data center (US, IN, EU, JP, SA, CA, AU, CN)
- `--client-id ID` - Zoho OAuth Client ID
- `--client-secret SECRET` - Zoho OAuth Client Secret
- `--debug` - Enable debug logging
- `--non-interactive` - Run in non-interactive mode
- `--help` - Show help information

## Tool Examples

### Get Applications
```json
{
  "name": "get_applications"
}
```

### Get Records (Quick View)
```json
{
  "name": "get_records_quick_view",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report"
  }
}
```

### Add Records
```json
{
  "name": "add_records",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "form_link_name": "your-form",
    "data": {
      "field_name": "field_value"
    }
  }
}
```

### Upload File
```json
{
  "name": "upload_file",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "record-id",
    "field_link_name": "file-field",
    "file_path": "/path/to/file.pdf"
  }
}
```

## Data Center Support

The server supports all Zoho data centers:

- **US**: zohoapis.com (default)
- **IN**: zohoapis.in
- **EU**: zohoapis.eu
- **JP**: zohoapis.jp
- **SA**: zohoapis.sa
- **CA**: zohoapis.ca
- **AU**: zohoapis.com.au
- **CN**: zohoapis.com.cn

## API Reference

### Meta APIs

#### `get_applications`
Get all applications that you have access to.

**Parameters:** None

**Example:**
```json
{
  "name": "get_applications"
}
```

#### `get_applications_by_workspace`
Get applications hosted in a given workspace that you have access to.

**Parameters:**
- `account_owner_name` (string, required): The account owner name

**Example:**
```json
{
  "name": "get_applications_by_workspace",
  "arguments": {
    "account_owner_name": "your-account"
  }
}
```

#### `get_sections`
Get information about sections and underlying components (forms, reports, pages) for an application.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name

**Example:**
```json
{
  "name": "get_sections",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app"
  }
}
```

#### `get_forms`
Get meta information of all forms present in a Zoho Creator application.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name

**Example:**
```json
{
  "name": "get_forms",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app"
  }
}
```

#### `get_reports`
Get meta information of all reports present in a Zoho Creator application.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name

**Example:**
```json
{
  "name": "get_reports",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app"
  }
}
```

#### `get_pages`
Get meta information of all pages present in a Zoho Creator application.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name

**Example:**
```json
{
  "name": "get_pages",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app"
  }
}
```

#### `get_fields`
Get meta information of all fields present in a form of a Zoho Creator application.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `form_link_name` (string, required): The form link name

**Example:**
```json
{
  "name": "get_fields",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "form_link_name": "your-form"
  }
}
```

### Data APIs

#### `get_records_quick_view`
Get records displayed by a report (quick view). Maximum 200 records per request.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `from` (integer, optional): Starting record number (minimum 1)
- `limit` (integer, optional): Maximum number of records to return (maximum 200)
- `criteria` (string, optional): Filter criteria for records
- `field_config` (string, optional): Field configuration
- `fields` (array, optional): Array of field names to include
- `max_records` (integer, optional): Maximum records to return (maximum 1000)

**Example:**
```json
{
  "name": "get_records_quick_view",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "limit": 50,
    "criteria": "Status == 'Active'"
  }
}
```

#### `get_record_detail_view`
Get data displayed in detail view of a specific record by ID.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `field_config` (string, optional): Field configuration
- `fields` (array, optional): Array of field names to include

**Example:**
```json
{
  "name": "get_record_detail_view",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345"
  }
}
```

#### `add_records`
Add one or more records to a form. Maximum 200 records per request.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `form_link_name` (string, required): The form link name
- `data` (array, required): Array of record objects to add
- `result` (object, optional): Result configuration
  - `fields` (array, optional): Fields to include in response
  - `message` (boolean, optional): Include message in response
  - `tasks` (boolean, optional): Include tasks in response

**Example:**
```json
{
  "name": "add_records",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "form_link_name": "your-form",
    "data": [
      {
        "Name": "John Doe",
        "Email": "john@example.com"
      }
    ]
  }
}
```

#### `update_records`
Update records displayed in a report. Maximum 200 records per request.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `data` (object, required): Data to update
- `criteria` (string, required): Filter criteria for records to update
- `result` (object, optional): Result configuration
  - `fields` (array, optional): Fields to include in response
  - `message` (boolean, optional): Include message in response
  - `tasks` (boolean, optional): Include tasks in response
- `process_until_limit` (boolean, optional): Process first 200 records if more than 200 match criteria

**Example:**
```json
{
  "name": "update_records",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "data": {
      "Status": "Updated"
    },
    "criteria": "Name == 'John Doe'"
  }
}
```

#### `update_record_by_id`
Update a specific record by ID.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `data` (object, required): Data to update
- `result` (object, optional): Result configuration
  - `fields` (array, optional): Fields to include in response
  - `message` (boolean, optional): Include message in response
  - `tasks` (boolean, optional): Include tasks in response

**Example:**
```json
{
  "name": "update_record_by_id",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345",
    "data": {
      "Status": "Updated"
    }
  }
}
```

#### `delete_records`
Delete records displayed by a report. Maximum 200 records per request.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `criteria` (string, required): Filter criteria for records to delete
- `result` (object, optional): Result configuration
  - `message` (boolean, optional): Include message in response
  - `tasks` (boolean, optional): Include tasks in response
- `process_until_limit` (boolean, optional): Process first 200 records if more than 200 match criteria

**Example:**
```json
{
  "name": "delete_records",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "criteria": "Status == 'Inactive'"
  }
}
```

#### `delete_record_by_id`
Delete a specific record by ID.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `result` (object, optional): Result configuration
  - `message` (boolean, optional): Include message in response
  - `tasks` (boolean, optional): Include tasks in response

**Example:**
```json
{
  "name": "delete_record_by_id",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345"
  }
}
```

### File APIs

#### `upload_file`
Upload a file to a file upload, image, audio, video, or signature field of a record.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `field_link_name` (string, required): The field link name
- `file_path` (string, required): Path to the file to upload

**Example:**
```json
{
  "name": "upload_file",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345",
    "field_link_name": "document_field",
    "file_path": "/path/to/document.pdf"
  }
}
```

#### `download_file`
Download a file from a file upload, image, audio, video, or signature field of a record.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `field_link_name` (string, required): The field link name

**Example:**
```json
{
  "name": "download_file",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345",
    "field_link_name": "document_field"
  }
}
```

#### `download_file_from_subform`
Download a file from a subform field of a record.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `subform_link_name` (string, required): The subform link name
- `field_link_name` (string, required): The field link name
- `subform_record_ID` (string, required): The subform record ID

**Example:**
```json
{
  "name": "download_file_from_subform",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345",
    "subform_link_name": "attachments",
    "field_link_name": "file_field",
    "subform_record_ID": "67890"
  }
}
```

### Bulk APIs

#### `create_bulk_read_job`
Create a bulk read job to export records (100k-200k records).

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `query` (object, required): Query configuration
  - `criteria` (string, optional): Filter criteria for records
  - `max_records` (integer, optional): Maximum records to export (100,000 to 200,000)
  - `fields` (array, optional): Array of field names to include

**Example:**
```json
{
  "name": "create_bulk_read_job",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "query": {
      "criteria": "Status == 'Active'",
      "max_records": 150000,
      "fields": ["Name", "Email", "Status"]
    }
  }
}
```

#### `get_bulk_read_job_status`
Get the status of a bulk read job.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `job_ID` (string, required): The bulk read job ID

**Example:**
```json
{
  "name": "get_bulk_read_job_status",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "job_ID": "bulk_job_123"
  }
}
```

#### `download_bulk_read_result`
Download the result of a bulk read job as CSV file.

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `job_ID` (string, required): The bulk read job ID

**Example:**
```json
{
  "name": "download_bulk_read_result",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "job_ID": "bulk_job_123"
  }
}
```

### Publish APIs

#### `publish_api_add_records`
Add records to a published form (public API).

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `form_link_name` (string, required): The form link name
- `privatelink` (string, required): Private link for published form
- `data` (array, required): Array of record objects to add
- `result` (object, optional): Result configuration
  - `fields` (array, optional): Fields to include in response
  - `message` (boolean, optional): Include message in response
  - `tasks` (boolean, optional): Include tasks in response

**Example:**
```json
{
  "name": "publish_api_add_records",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "form_link_name": "your-form",
    "privatelink": "your-private-link",
    "data": [
      {
        "Name": "John Doe",
        "Email": "john@example.com"
      }
    ]
  }
}
```

#### `publish_api_get_records_quick_view`
Get records from a published report (public API).

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `privatelink` (string, required): Private link for published report
- `from` (integer, optional): Starting record number (minimum 1)
- `limit` (integer, optional): Maximum number of records to return (maximum 200)
- `criteria` (string, optional): Filter criteria for records
- `field_config` (string, optional): Field configuration
- `fields` (array, optional): Array of field names to include
- `max_records` (integer, optional): Maximum records to return (maximum 1000)

**Example:**
```json
{
  "name": "publish_api_get_records_quick_view",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "privatelink": "your-private-link",
    "limit": 50
  }
}
```

#### `publish_api_get_record_detail_view`
Get record detail view from a published report (public API).

**Parameters:**
- `account_owner_name` (string, required): The account owner name
- `app_link_name` (string, required): The application link name
- `report_link_name` (string, required): The report link name
- `record_ID` (string, required): The record ID
- `privatelink` (string, required): Private link for published report
- `field_config` (string, optional): Field configuration
- `fields` (array, optional): Array of field names to include

**Example:**
```json
{
  "name": "publish_api_get_record_detail_view",
  "arguments": {
    "account_owner_name": "your-account",
    "app_link_name": "your-app",
    "report_link_name": "your-report",
    "record_ID": "12345",
    "privatelink": "your-private-link"
  }
}
```

## Project Structure

```
zoho-creator-mcp-server/
├── src/
│   ├── index.ts          # Main server implementation
│   ├── tools.ts          # Tool implementations
│   ├── tool-schema.ts    # Tool definitions
│   └── ...
├── dist/                 # Compiled output
├── bin/                  # CLI executable
├── logs/                 # Log files
├── package.json
└── README.md
```

## Development

### Setup

```bash
git clone <repository>
cd zoho-creator-mcp-server
npm install
npm run build
```

### Testing

```bash
npm test
```

### Debugging

```bash
npx zoho-creator-mcp-server --debug
```

Debug logs are written to: `/tmp/zoho-creator-mcp-debug.log`

## Authentication

To use this server, you need a valid Zoho OAuth2 access token. Follow these steps:

1. Create a Zoho application in the [Zoho Developer Console](https://accounts.zoho.com/developerconsole)
2. Configure OAuth2 settings
3. Generate access token
4. Set the token in environment variables or command line

## Support

For issues and support, please check:
- Zoho Creator API documentation
- GitHub issues
- Debug logs for troubleshooting
