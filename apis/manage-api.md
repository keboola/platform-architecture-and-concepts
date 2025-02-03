# Manage API

The Manage API is a core API of the Keboola platform that provides management capabilities for projects, organizations, and users. It serves as the primary interface for administrative operations and project management.

## Overview

The Manage API provides a RESTful interface for:
- Managing projects and organizations
- User management and access control
- Component and configuration management
- Monitoring and maintenance operations

## Authentication

Authentication is done via Management Token, which must be included in the `X-KBC-ManageApiToken` HTTP header with each API call.

## Key Features

1. **Project Management**
   - Create and delete projects
   - Modify project settings
   - Manage project limits
   - Handle project tokens

2. **Organization Management**
   - Create and manage organizations
   - Handle organization settings
   - Manage organization members

3. **User Management**
   - Manage user access
   - Handle user invitations
   - Modify user permissions

4. **Component Management**
   - List available components
   - Manage component configurations
   - Handle component updates

## Related Tools

The Manage API is accessible through various clients:
- [Keboola CLI](../clients/cli.md) - Command-line interface for project management
- Web user interface
- API clients

## Related Concepts

- [Component](../concepts/component.md)
- [Configuration](../concepts/configuration.md)
- [Token](../concepts/token.md)
- [Development Branch](../concepts/development-branch.md)
- [Workspace](../concepts/workspace.md)

## Best Practices

1. Use appropriate authentication tokens
2. Implement proper error handling
3. Follow rate limiting guidelines
4. Monitor API responses
5. Implement proper logging
6. Use appropriate project organization
7. Manage access rights carefully

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
- 503 `Service Unavailable`: System maintenance
