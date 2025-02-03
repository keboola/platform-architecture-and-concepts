# Table Alias

A Table Alias in Keboola is a unique concept that behaves similarly to database views, providing a read-only reference to another table with optional filtering and column selection capabilities.

## Key Features

1. **Read-Only Views**
   - Acts as a database view
   - Provides read-only access to source data
   - Cannot be chained (no aliases of aliases)
   - Works only between buckets with same backend

2. **Data Filtering**
   - Supports row-level filtering
   - Uses simple comparison operators (eq, ne)
   - Allows multiple filter values
   - Filters can be modified or removed

3. **Column Management**
   - Automatic column synchronization with source
   - Optional manual column selection
   - Ability to enable/disable auto-sync
   - Dynamic column updates

4. **Usage Restrictions**
   - Cannot create from external bucket tables
   - Cannot create from development branch tables
   - Cannot create in external buckets
   - Source and target must share backend type

## Use Cases

1. **Data Sharing**
   - Share subset of data between projects
   - Provide filtered views of master data
   - Create specialized views for different users

2. **Data Organization**
   - Create logical views of data
   - Simplify complex data structures
   - Provide focused data views

## Best Practices

1. Use meaningful alias names
2. Document filter conditions
3. Consider column synchronization needs
4. Monitor source table changes
5. Use appropriate access controls
6. Regular validation of alias integrity 

## Related Concepts
- [Table](./table.md)
- [Bucket](./bucket.md)
- [Metadata](./metadata.md)
- [Development Branch](./development-branch.md) 