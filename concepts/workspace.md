# Workspace

A Workspace in Keboola is a secure, isolated environment that provides computational resources and direct access to data stored in Storage. It's a unique concept that bridges the gap between data storage and data processing.

## Key Characteristics

1. **Isolation**
   - Secure, isolated environment
   - Dedicated computational resources
   - Temporary storage allocation
   - Project-specific access

2. **Data Access**
   - Direct access to Storage tables
   - Input/Output mapping capabilities
   - Secure data transfer
   - Support for various backends (Snowflake, Synapse, etc.)

3. **Lifecycle**
   - Created on demand
   - Time-limited existence
   - Automatic cleanup
   - Resource management

4. **Use Cases**
   - Data transformation development
   - Ad-hoc analysis
   - Machine learning model development
   - Interactive data exploration

## Types of Workspaces

1. **Transformation Workspaces**
   - Used for developing and testing transformations
   - Support multiple backend types
   - Integrated with transformation service

2. **Jupyter Workspaces**
   - Interactive Python/R development
   - Data science and analysis
   - Notebook-based interface

## Best Practices

1. Always clean up workspaces after use
2. Use appropriate sizing for resources
3. Implement proper data loading strategies
4. Monitor workspace expiration
5. Follow security guidelines for credential management

## Related Concepts
- [Component](./component.md)
- [Configuration](./configuration.md)
- [Development Branch](./development-branch.md)
- [Table](./table.md)
