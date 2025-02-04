# Output Mapping

Output Mapping in Keboola defines how data produced by a component is stored back into Storage. It specifies which tables and files should be saved after component execution.

## Overview

Output Mapping is a configuration that:
- Defines data destinations in Storage
- Specifies column mapping and data types
- Controls data writing behavior
- Supports both tables and files

## Types of Output Mapping

1. **Table Output Mapping**
   - Destination table definition
   - Column mapping
   - Primary key definition
   - Incremental loading
   - Data type conversion
   - Delete rows options

2. **File Output Mapping**
   - File naming patterns
   - Tag management
   - Compression options
   - Manifest file generation
   - File format settings

## Configuration Options

1. **Table Options**
   - Destination table
   - Primary key columns
   - Delete rows
   - Incremental loading
   - Column types
   - Column mapping
   - Table metadata

2. **File Options**
   - File name/pattern
   - Tags
   - Is public flag
   - Is permanent flag
   - Manifest options
   - Compression settings

## Best Practices

1. Define clear naming conventions
2. Set appropriate primary keys
3. Use incremental loading when possible
4. Manage table metadata
5. Document mapping configurations
6. Monitor storage usage
7. Regular cleanup of temporary files

## Related Concepts
- [Input Mapping](./input-mapping.md)
- [Configuration](./configuration.md)
- [Component](./component.md)
- [Storage](./storage.md)
- [Table](./table.md)
- [File Storage](./file-storage.md)

## Related APIs
- [Storage API](../apis/storage-api.md)
