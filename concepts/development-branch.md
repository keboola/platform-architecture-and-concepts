# Development Branch

A Development Branch in Keboola is a unique concept that allows safe development and testing of changes in a project without affecting the production environment. It provides an isolated copy of selected project elements where modifications can be tested before deployment to production.

## Key Features

1. **Isolation**
   - Separate environment for development
   - Independent configurations and transformations
   - Isolated data storage
   - No impact on production

2. **Scope**
   - Configurations
   - Transformations
   - Orchestrations
   - Storage buckets (selected)

3. **Data Handling**
   - Selective data copying from production
   - Independent data loading
   - Separate storage tables
   - Isolated workspaces

4. **Security**
   - Separate access control
   - Independent tokens
   - Protected production environment
   - Controlled promotion process

## Usage Patterns

1. **Development Flow**
   - Create development branch
   - Copy required configurations
   - Make and test changes
   - Promote to production

2. **Testing**
   - Test new configurations
   - Validate transformations
   - Verify orchestrations
   - Check data quality

## Best Practices

1. Use meaningful branch names
2. Copy only necessary components
3. Regular testing of changes
4. Document modifications
5. Clean up unused branches
6. Validate before promotion to production

## Related Concepts
- [Component](./component.md)
- [Configuration](./configuration.md)
- [Workspace](./workspace.md)
- [Table](./table.md)
- [Bucket](./bucket.md)
