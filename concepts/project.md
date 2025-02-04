# Project

A Project in Keboola is a container for all user resources including data, configurations, and transformations. It represents an isolated workspace where users can work with data and build data pipelines.

## Overview

Projects are fundamental units in Keboola that:
- Belong to an organization
- Have their own storage
- Maintain separate user access control
- Have independent billing and resource limits
- Can be in different stacks

## Key Features

1. **Resource Management**
   - Storage buckets and tables
   - Component configurations
   - Transformations
   - Orchestrations
   - Schedules
   - Data apps

2. **Access Control**
   - Project-specific users and tokens
   - Role-based access control
   - MFA enforcement (inherited from organization)
   - Token management

3. **Limits and Power**
   - Storage size limits
   - Concurrent jobs limits
   - Transformation resources
   - API rate limits
   - Data retention settings

4. **Project Operations**
   - Data takeout
   - Project deletion
   - Project migration between stacks
   - Project backup and restore

## Development Features

1. **Development Branches**
   - Isolated development environments
   - Branch-specific configurations
   - Safe testing environment
   - Merge to production

2. **Workspaces**
   - Interactive data exploration
   - Transformation development
   - Temporary computational resources
   - Various backend support (Snowflake, Synapse, etc.)

## Best Practices

1. Use meaningful project names
2. Implement proper access control
3. Monitor resource usage
4. Regular cleanup of unused resources
5. Document project structure
6. Use development branches for changes
7. Maintain backup strategies

## Related Concepts
- [Organization](./organization.md)
- [Stack](./stack.md)
- [Bucket](./bucket.md)
- [Configuration](./configuration.md)
- [Development Branch](./development-branch.md)
- [Workspace](./workspace.md)

## Related APIs
- [Manage API](../apis/manage-api.md)
- [Storage API](../apis/storage-api.md)
