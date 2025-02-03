# File Storage

File Storage in Keboola is a unique concept that provides temporary or permanent storage for arbitrary files within the platform. It serves as an intermediary storage system for data import/export operations and file exchange between components.

## Key Features

1. **Storage Types**
   - Temporary storage (15-day retention)
   - Permanent storage (on request)
   - Encrypted storage
   - Private access control

2. **File Management**
   - File tagging system
   - RunId tracking
   - Size monitoring
   - Expiration management
   - Access URL generation

3. **Security**
   - Encrypted storage
   - Private access
   - Time-limited access URLs
   - Token-based authentication
   - Secure file transfer

4. **Integration Features**
   - Component data exchange
   - Import/Export operations
   - Backup storage
   - Project file sharing
   - Data transformation support

## Usage Patterns

1. **Data Operations**
   - Table data import staging
   - Export result storage
   - Data backup storage
   - Component file exchange
   - Project file sharing

2. **Access Management**
   - Temporary URL generation
   - Access control
   - File lifecycle management
   - Storage optimization

## Best Practices

1. Monitor file expiration dates
2. Use appropriate file tags
3. Clean up unnecessary files
4. Implement proper error handling
5. Use compression when appropriate
6. Track file metadata
7. Follow security guidelines 

## Related Concepts
- [Component](./component.md)
- [Token](./token.md)
- [Metadata](./metadata.md)
- [Artifact](./artifact.md) 