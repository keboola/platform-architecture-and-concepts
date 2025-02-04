# Keboola Platform Architecture and Concepts

This repository contains documentation of Keboola Platform architecture and concepts. Keboola is a cloud-based data platform that enables users to extract, transform, and load data from various sources. The platform is built on a microservices architecture, with each service responsible for specific functionality.

## Repository Structure

- [`/apis`](./apis) - API documentation and specifications
- [`/clients`](./clients) - Client implementations and usage
- [`/concepts`](./concepts) - Core concepts and building blocks
- [`/services`](./services) - Service-specific documentation

## Concepts

- [Artifact](./concepts/artifact.md) - Build and deployment artifacts
- [Bucket](./concepts/bucket.md) - Data organization units
- [Component](./concepts/component.md) - Modular units that perform specific tasks
- [Configuration](./concepts/configuration.md) - Defines how components are configured
- [Development Branch](./concepts/development-branch.md) - Isolated development environment
- [Encryption](./concepts/encryption.md) - Data security and protection
- [Event](./concepts/event.md) - System and user action tracking
- [File Storage](./concepts/file-storage.md) - Binary and file data management
- [Input Mapping](./concepts/input-mapping.md) - Data input configuration
- [Job](./concepts/job.md) - Execution units for processing tasks
- [Maintainer](./concepts/maintainer.md) - Entity managing organizations and projects
- [Metadata](./concepts/metadata.md) - Additional information about data and objects
- [Orchestration](./concepts/orchestration.md) - Process automation and scheduling
- [Organization](./concepts/organization.md) - Group of related projects
- [Output Mapping](./concepts/output-mapping.md) - Data output configuration
- [Project](./concepts/project.md) - Container for user resources
- [Stack](./concepts/stack.md) - Independent platform instance
- [Storage](./concepts/storage.md) - Data storage system
- [Storage Backend](./concepts/storage-backend.md) - Underlying storage technology
- [Storage Job](./concepts/storage-job.md) - Specialized jobs for data storage operations
- [Table](./concepts/table.md) - Data storage structure
- [Table Alias](./concepts/table-alias.md) - Virtual table references
- [Ticket](./concepts/ticket.md) - Support and issue tracking
- [Token](./concepts/token.md) - Authentication and authorization
- [Transformation](./concepts/transformation.md) - Data processing and analysis
- [Trigger](./concepts/trigger.md) - Automated execution conditions
- [Variable](./concepts/variable.md) - Dynamic configuration placeholders
- [Workspace](./concepts/workspace.md) - Development and analysis environments

## APIs

- [Billing API](./apis/billing-api.md) - Usage tracking and billing
- [Encryption API](./apis/encryption-api.md) - Data encryption and key management
- [Importer API](./apis/importer-api.md) - Data import operations
- [Manage API](./apis/manage-api.md) - Project and organization management
- [Queue API](./apis/queue-api.md) - Job queueing and execution
- [Storage API](./apis/storage-api.md) - Data storage and management
- [Transformation API](./apis/transformation-api.md) - Data transformation operations

## Services

- [Billing Service](./services/billing.md) - Manages usage tracking and billing
- [Connection Service](./services/connection.md) - Manages connections to external systems
- [Encryption Service](./services/encryption.md) - Handles data encryption
- [Queue Service](./services/queue.md) - Handles job execution
- [Storage Service](./services/storage.md) - Manages data storage
- [Transformation Service](./services/transformation.md) - Handles data transformations

## Clients

- [Command Line Interface (CLI)](./clients/cli.md) - Command-line tools
- [Python Client](./clients/python-client.md) - Python SDK
- [Storage API Python Client](./clients/storage-api-python-client.md) - Python client for Storage API
- [User Interface (UI)](./clients/ui.md) - Web-based user interface

## Contributing

When contributing to this documentation:

1. Each concept should have its own file in the `concepts` folder
2. Each API should have its own file in the `apis` folder with related concepts listed
3. Each service should have its own file in the `services` folder with supported APIs listed
4. Each API documentation must include an OpenAPI Specification section
5. API documentation should focus on authentication and general information, not endpoints
6. Maintain proper cross-references between related concepts using Markdown links
7. Follow consistent formatting and style throughout the documentation
