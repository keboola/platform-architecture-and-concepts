# Storage Token

A Storage Token in Keboola is a unique security concept that combines authentication and authorization into a single entity. It provides granular access control to various Storage API resources and capabilities.

## Key Features

1. **Token Types**
   - Master Token (one per project admin)
   - Regular Storage Tokens (limited privileges)
   - Component-specific Tokens
   - Development Branch Tokens

2. **Access Control**
   - Bucket-level permissions (read/write/manage)
   - Component access control
   - File upload management
   - Token management capabilities
   - Branch management privileges

3. **Security Features**
   - Expiration settings
   - Daily capacity limits
   - Token refreshing
   - Disable/Enable functionality
   - Audit trail

4. **Capabilities**
   - Storage bucket management
   - Token management
   - File upload access
   - Trash management
   - Protected branch management
   - Job creation privileges

## Usage Patterns

1. **Project Administration**
   - Master tokens for storage management
   - Component-specific access
   - Development environment access
   - Cross-project data sharing

2. **Security Management**
   - Granular storage permission control
   - Resource access limitation
   - Time-based access control
   - Usage monitoring

## Best Practices

1. Use descriptive token names
2. Implement least privilege principle
3. Regular token rotation
4. Monitor token usage
5. Document token purposes
6. Review token permissions regularly
7. Set appropriate expiration dates

## Related Concepts
- [Bucket](./bucket.md)
- [Component](./component.md)
- [Development Branch](./development-branch.md)
- [File Storage](./file-storage.md) 