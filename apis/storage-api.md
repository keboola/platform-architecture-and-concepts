# Storage API

The Storage API is the core API for managing data in Keboola Platform.

## Overview

The Storage API provides functionality for managing buckets, tables, files, and workspaces. It is the primary interface for data storage and retrieval operations in the platform.

## OpenAPI Specification

Available at: https://storage.north-europe.azure.keboola.com/docs/swagger.yaml

## Authentication

The API uses a dedicated token for authentication:
- `X-StorageApi-Token`: Required for all operations
- Token can be generated in the Keboola UI with specific permissions
- Different token types available:
  - Master token
  - Component token
  - Read-only token

## Key Features

- Bucket management
- Table operations
- File uploads and downloads
- Data import and export
- Metadata management
- Workspace handling

## Related Concepts

- [Bucket](../concepts/bucket.md)
- [Table Alias](../concepts/table-alias.md)
- [Input Mapping](../concepts/input-mapping.md)
- [Output Mapping](../concepts/output-mapping.md)
- [Data Integration](../concepts/data-integration.md)
- [Data Transformation](../concepts/data-transformation.md)

## Related APIs

- [Queue API](queue-api.md)
- [Transformation API](transformation-api.md)

## Services

The Storage API is managed by the following services:
- Storage Service
- File Service
- Workspace Service

## Data Format Requirements

- CSV files must follow [RFC4180 Specification](http://tools.ietf.org/html/rfc4180)
- Files must use `UTF-8` encoding
- Files can be sent uncompressed or gzipped

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