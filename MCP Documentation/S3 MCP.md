# S3 MCP Server

An MCP (Model Context Protocol) server that provides AWS S3 file upload capabilities with automatic file type routing. HTML files are uploaded to one bucket and Python files to another bucket.

## Features

- **Automatic File Type Detection**: Automatically detects HTML (.html, .htm) and Python (.py) files
- **Dual Bucket Support**: Routes HTML files to one bucket and Python files to another
- **Public URL Generation**: Returns public S3 URLs for uploaded files
- **MCP Integration**: Works seamlessly with Claude and other MCP-compatible clients
- **Resource Support**: Provides access to QA README documentation as a resource

## Installation

```bash
npm install
npm run build
```

## Configuration

Set up your environment variables by copying `.env.example` to `.env`:

```bash
cp .env.example .env
```

Required environment variables:

```bash
# AWS Configuration
AWS_ACCESS_KEY_ID=your_access_key_here
AWS_SECRET_ACCESS_KEY=your_secret_key_here
AWS_REGION=us-east-1

# S3 Bucket Configuration
HTML_BUCKET=your-html-bucket-name
PYTHON_BUCKET=your-python-bucket-name

# GitHub Configuration (for upload_file_to_github tool)
GITHUB_ACCESS_TOKEN=your_github_personal_access_token
GITHUB_OWNER=your_github_username_or_org
GITHUB_REPO_NAME=your_repository_name
GITHUB_BRANCH=main
```

## Usage

### Command Line

```bash
# Start with environment variables
npm start

# Start with command line arguments
npx s3-mcp-server --aws-access-key-id YOUR_KEY --aws-secret-access-key YOUR_SECRET --html-bucket my-html-bucket --python-bucket my-python-bucket

# Debug mode
npx s3-mcp-server --debug
```

### Available Tools

The server provides the following MCP tools:

#### 1. `upload_file_to_s3`

Uploads a file to the appropriate S3 bucket based on file type.

**Parameters:**
- `file_content` (string, required): The content of the file to upload
- `file_name` (string, required): The name of the file including extension
- `file_type` (string, optional): Either "html" or "py" (auto-detected if not provided)
- `encoding` (string, optional): Either "utf8" or "base64" (default: "utf8")

**Response:**
Returns a JSON object containing:
- `success`: Upload status
- `fileName`: Original file name
- `fileType`: Detected file type
- `bucket`: S3 bucket used
- `key`: S3 object key
- `url`: Public S3 URL
- `contentType`: MIME type
- `uploadedAt`: Upload timestamp

**Example Usage:**
```javascript
// Upload an HTML file
{
  "tool": "upload_file_to_s3",
  "arguments": {
    "file_content": "<html><body><h1>Hello World</h1></body></html>",
    "file_name": "index.html"
  }
}

// Upload a Python file
{
  "tool": "upload_file_to_s3",
  "arguments": {
    "file_content": "print('Hello World')",
    "file_name": "script.py"
  }
}
```

#### 2. `list_s3_config`

Lists the current S3 configuration.

**Parameters:** None

**Response:**
Returns configuration details including:
- `region`: AWS region
- `htmlBucket`: HTML files bucket name
- `pythonBucket`: Python files bucket name
- `awsCredentialsSet`: Whether AWS credentials are configured
- `supportedFileTypes`: List of supported file types

#### 3. `list_bucket_contents`

Lists all files and folders in the S3 bucket.

**Parameters:**
- `bucket_type` (string, optional): Which bucket to list ("html", "python", or "both")
- `prefix` (string, optional): Optional prefix to filter objects
- `max_keys` (number, optional): Maximum number of objects to return (1-1000)

#### 4. `list_dags_contents`

Lists all Python files in the dags/ folder of the Python bucket.

**Parameters:**
- `max_keys` (number, optional): Maximum number of objects to return (1-1000)

#### 5. `clear_cache`

Clears Redis cache by calling the dev.siya.com Redis delete endpoint.

**Parameters:**
- `key` (string, optional): Redis key to delete (empty for all keys)
- `endpoint_url` (string, optional): Redis delete endpoint URL

#### 6. `upload_file_to_github`

Uploads a file to a GitHub repository (including private repos). Uses GitHub API with access token, repo name, and branch from environment variables.

**Parameters:**
- `file_content` (string, required): The content of the file to upload
- `file_name` (string, required): The name of the file including extension
- `filePath` (string, optional): Path to the file to upload (alternative to file_content + file_name)
- `commit_message` (string, optional): Custom commit message for the upload
- `encoding` (string, optional): Either "utf8" or "base64" (default: "utf8")

**Response:**
Returns a JSON object containing:
- `success`: Upload status
- `fileName`: Original file name
- `repository`: GitHub repository (owner/repo)
- `branch`: Target branch
- `commitSha`: Commit SHA
- `fileSha`: File SHA
- `fileUrl`: GitHub file URL
- `rawUrl`: Raw file URL
- `commitMessage`: Commit message used
- `uploadedAt`: Upload timestamp
- `fileSize`: File size in bytes
- `encoding`: Encoding used

**Example Usage:**
```javascript
// Upload a file with content
{
  "tool": "upload_file_to_github",
  "arguments": {
    "file_content": "console.log('Hello from GitHub!');",
    "file_name": "script.js",
    "commit_message": "Add new JavaScript file"
  }
}

// Upload a file from disk
{
  "tool": "upload_file_to_github",
  "arguments": {
    "filePath": "/path/to/local/file.txt",
    "commit_message": "Upload local file"
  }
}
```

**Required Environment Variables:**
- `GITHUB_ACCESS_TOKEN`: Your GitHub personal access token
- `GITHUB_OWNER`: GitHub username or organization name
- `GITHUB_REPO_NAME`: Repository name
- `GITHUB_BRANCH`: Target branch (defaults to 'main')

#### 7. `convert_s3_url_to_presigned_url`

Converts an S3 URL to a presigned URL that is valid for 3 days (72 hours) by default. Useful for sharing files with temporary access without making them permanently public.

**Parameters:**
- `s3_url` (string, required): The S3 URL to convert. Format: `https://bucket-name.s3.region.amazonaws.com/key`
- `expiration_hours` (number, optional): Number of hours the presigned URL should be valid (1-168 hours, default: 72)

**Response:**
Returns a JSON object containing:
- `success`: Conversion status
- `originalUrl`: Original S3 URL
- `presignedUrl`: Generated presigned URL
- `bucket`: S3 bucket name
- `key`: S3 object key
- `region`: AWS region
- `expirationHours`: Hours until expiration
- `expirationTime`: Exact expiration timestamp
- `generatedAt`: Generation timestamp
- `note`: Description of the URL validity

**Example Usage:**
```javascript
// Convert S3 URL to presigned URL (3 days)
{
  "tool": "convert_s3_url_to_presigned_url",
  "arguments": {
    "s3_url": "https://nyk-etl-prod.s3.ap-south-1.amazonaws.com/index.html",
    "expiration_hours": 72
  }
}

// Convert with custom expiration (1 day)
{
  "tool": "convert_s3_url_to_presigned_url",
  "arguments": {
    "s3_url": "https://nyk-etl-prod.s3.ap-south-1.amazonaws.com/document.pdf",
    "expiration_hours": 24
  }
}
```

## Available Resources

The server provides the following MCP resources:

### QA README Resource

Access comprehensive documentation for creating QNA (App Insights) workflows.

**Resource Details:**
- **URI**: `file://QA_README.md`
- **Name**: QA README
- **Description**: Complete guide for creating QNA (App Insights) with multi-step tasks involving MongoDB, HTML dashboards, Python-Airflow DAG conversion, S3 uploads, and metadata updates.
- **MIME Type**: `text/markdown`

**Example Usage:**
```javascript
// List available resources
{
  "method": "resources/list"
}

// Read QA README content
{
  "method": "resources/read",
  "params": {
    "uri": "file://QA_README.md"
  }
}
```

The QA README resource contains detailed documentation for:
- MongoDB operations (menu_files, workspace_menus, vesselinfos collections)
- HTML dashboard creation with Python scripts
- GitHub repository integration
- S3 upload and Airflow DAG conversion
- Cache management procedures
- Database environment overview (NYK_APP_URL, NYK_ETL_URL, NYK_PROD_URL)

For detailed usage instructions, see [RESOURCE_USAGE.md](./RESOURCE_USAGE.md).

## File Type Detection

The server automatically detects file types based on file extensions:

- **HTML Files**: `.html`, `.htm` → Uploaded to HTML bucket
- **Python Files**: `.py`, `.python` → Uploaded to Python bucket

Unsupported file types will return an error.

## S3 Bucket Setup

Make sure your S3 buckets are configured properly:

1. **Create Buckets**: Create two S3 buckets (one for HTML files, one for Python files)
2. **Set Permissions**: Configure bucket policies for public read access if needed
3. **CORS Configuration**: Set up CORS if files will be accessed from web browsers

Example bucket policy for public read access:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::your-bucket-name/*"
        }
    ]
}
```

## Development

```bash
# Development mode with auto-reload
npm run dev

# Build TypeScript
npm run build

# Clean build artifacts
npm run clean
```

## Troubleshooting

### Common Issues

1. **AWS Credentials Error**: Make sure `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` are set
2. **Bucket Access Error**: Verify bucket names and that your AWS credentials have S3 permissions
3. **Build Errors**: Run `npm run clean && npm run build` to rebuild from scratch

### Debug Mode

Enable debug mode to see detailed logs:

```bash
npx s3-mcp-server --debug
```

Debug logs are written to a temporary file (path shown in console output).

## Security Notes

- Never commit AWS credentials to version control
- Use IAM roles with minimal required permissions
- Consider using AWS IAM policies to restrict bucket access
- Be cautious with public bucket policies
