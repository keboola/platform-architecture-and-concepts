# Keboola Platform Architecture and Concepts

This repository contains comprehensive documentation about Keboola Platform's architecture, concepts, APIs, and services. It serves as the primary reference for understanding how different parts of the Keboola Platform work together.

## Repository Structure

- [`/concepts`](./concepts) - Core concepts and building blocks of the Keboola Platform
- [`/apis`](./apis) - API documentation and specifications
- [`/services`](./services) - Service-specific documentation
- [`/clients`](./clients) - Client implementations and usage

## Core Concepts

The Keboola Platform is built around several key concepts:

- [Configuration](./concepts/configuration.md) - Defines how platform components are configured
- [Component](./concepts/component.md) - Modular units that perform specific tasks
- [Job](./concepts/job.md) - Execution units for processing tasks
- [Storage Job](./concepts/storage-job.md) - Specialized jobs for data storage operations
- [Bucket](./concepts/bucket.md) - Data organization units
- [Workspace](./concepts/workspace.md) - Development and analysis environments
- [Input/Output Mapping](./concepts/input-mapping.md) - Data flow configuration
- [Metadata](./concepts/metadata.md) - Additional information about data and objects
- [Events](./concepts/event.md) - System and user action tracking
- [Artifacts](./concepts/artifact.md) - Build and deployment artifacts
- [File Storage](./concepts/file-storage.md) - Binary and file data management
- [Table Alias](./concepts/table-alias.md) - Virtual table references

## APIs

The platform exposes several APIs for different purposes:

- [Storage API](./apis/storage-api.md) - Data storage and management
- [Management API](./apis/manage-api.md) - Platform configuration and control
- [Billing API](./apis/billing-api.md) - Usage tracking and billing

## Services

Key platform services include:

- [Connection Service](./services/connection.md) - Manages data source connections

## Clients

Available client implementations:

- [Command Line Interface (CLI)](./clients/cli.md) - Command-line tools
- [User Interface (UI)](./clients/ui.md) - Web-based user interface

## Contributing

When contributing to this documentation:

1. Each concept should have its own file in the `concepts` folder
2. Maintain proper cross-references between related concepts using Markdown links
3. Each API should have its own file in the `api` folder with related concepts listed
4. Each service should have its own file in the `services` folder with supported APIs listed
5. Follow consistent formatting and style throughout the documentation
