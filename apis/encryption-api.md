# Encryption API

The Encryption API handles secure data encryption tasks in Keboola Platform.

## Overview

The Encryption API provides functionality for managing secure data encryption, key management, and credential storage. It ensures secure handling of sensitive information across the platform.

## OpenAPI specification

Available at: https://encryption.north-europe.azure.keboola.com/docs/swagger.yaml

## Authentication

The API uses the following authentication methods:
- `X-StorageApi-Token`: Required for basic operations
- `X-KBC-ManageApiToken` with scope "encryption:manage": Required for key management operations

## Key Features

- Key management
- Data encryption
- Secure configuration storage
- Credential management

## Related Concepts

- [Data Governance](../concepts/data-governance.md)
- [Configuration](../concepts/configuration.md)
- [Component](../concepts/component.md)

## Related APIs

- [Storage API](storage-api.md)
- [Manage API](manage-api.md)

## Services

The Encryption API is managed by the following services:
- Encryption Service
- Key Management Service 