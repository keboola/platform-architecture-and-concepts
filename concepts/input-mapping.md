# Input Mapping

Input Mapping in Keboola defines how data is loaded into a component from Storage. It specifies which tables and files should be available to the component during its execution.

## Overview

Input Mapping is a configuration that:
- Defines data sources for component execution
- Specifies data filtering and column selection
- Controls data loading behavior
- Supports both tables and files

## Types of Input Mapping

1. **Table Input Mapping**
   - Source table selection
   - Column selection
   - Data filtering
   - Days/Rows filtering
   - Changed in last X days filtering
   - Incremental processing

2. **File Input Mapping**
   - File selection by tags
   - File filtering by creation date
   - File filtering by name
   - Support for multiple file formats

## Configuration Options

1. **Table Options**
   - Source table
   - Destination table name
   - Columns to load
   - Where clause filtering
   - Days/Rows filtering
   - Changed in filtering
   - Data types

2. **File Options**
   - Tags
   - Query parameters
   - Processing options
   - Compression settings
   - File format settings

## Best Practices

1. Use descriptive mapping names
2. Implement efficient filtering
3. Select only needed columns
4. Use incremental processing when possible
5. Document mapping configurations
6. Regular mapping maintenance
7. Monitor mapping performance

## Related Concepts
- [Output Mapping](./output-mapping.md)
- [Configuration](./configuration.md)
- [Component](./component.md)
- [Storage](./storage.md)
- [Table](./table.md)
- [File Storage](./file-storage.md)

## Related APIs
- [Storage API](../apis/storage-api.md)
