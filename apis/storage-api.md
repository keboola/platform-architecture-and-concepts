# Storage API

Storage API is the core API of the Keboola platform that provides access to all data storage capabilities. It serves as the primary interface for managing data, tables, buckets, and workspaces within the Keboola environment.

## Overview

The Storage API provides a RESTful interface for:
- Importing and exporting CSV data to/from Table Storage
- Loading files into File Storage
- Managing tables, including keys and indexes
- Managing buckets and sharing
- Managing component configurations

## Data Format Requirements

- CSV files must follow [RFC4180 Specification](http://tools.ietf.org/html/rfc4180)
- Files must use `UTF-8` encoding
- Files can be sent uncompressed or gzipped

## Authentication

Authentication is done via Storage API Token, which must be included in the `X-StorageApi-Token` HTTP header with each API call. The token provides both authentication and authorization to a single project.

## HTTP Headers

Supported headers:
- `X-StorageApi-Token`: Authentication token
- `X-KBC-RunId`: ID for matching component runs with storage jobs
- `X-KBC-Backend`: Customization of backend configuration (JSON string)

## Response Codes

### Success Responses
- 200 `OK`: Request successful
- 201 `Created`: Resource created
- 202 `Accepted`: Asynchronous task accepted
- 204 `No Content`: Successful request with no content

### Error Responses
- 400 `Bad Request`: Invalid input data
- 401 `Unauthorized`: Authentication failed
- 403 `Forbidden`: Access denied
- 404 `Not Found`: Resource doesn't exist
- 500 `Internal Server Error`: System error
- 503 `Temporary Unavailable`: System maintenance

## Synchronous vs Asynchronous Operations

Long-running operations are handled asynchronously:
- Table data loading
- Snapshotting
- Data export
- Table structure modifications

Asynchronous operations return:
- HTTP 202 status code
- Job Resource reference
- Progress can be monitored via Job Resource URL

## Partial Responses

API supports partial responses through the `include` parameter to return only specific resources in the response.

## Rate Limiting

The API implements rate limiting for system stability:
- Clients should implement retry mechanisms
- Use exponential backoff for retries
- Monitor response headers for rate limit information

## Best Practices

1. Use incremental loading when possible
2. Implement proper error handling
3. Follow rate limiting guidelines
4. Use appropriate compression for data transfers
5. Monitor job status for asynchronous operations
6. Properly manage authentication tokens
7. Validate data before import
8. Use appropriate bucket organization
9. Implement proper logging and monitoring

## Key Features

1. **Table Management**
   - Create and delete tables
   - Import and export data
   - Manage table metadata
   - Handle table aliases

2. **Bucket Operations**
   - Create and organize buckets
   - Set bucket properties
   - Manage sharing and access rights

3. **Data Operations**
   - Bulk data loading
   - Incremental data loading
   - Data preview and exploration
   - Data validation

4. **Workspace Management**
   - Create and delete workspaces
   - Load data into workspaces
   - Manage workspace access

5. **File Storage**
   - Upload and download files
   - Manage file tags
   - Handle file permissions

## Data Types

The Storage API supports various data types:
- STRING
- INTEGER
- DECIMAL
- DATE
- TIMESTAMP
- BOOLEAN

## Rate Limiting

The API implements rate limiting to ensure fair usage and system stability. Clients should implement appropriate retry mechanisms with exponential backoff. 