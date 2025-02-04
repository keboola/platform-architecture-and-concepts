# Billing API

The Billing API calculates a consumed and remaining credits of Keboola Platform PAYG users.

## Overview

The Billing API provides functionality for managing credits, recording durations of various operations, and handling marketplace subscriptions. It is essential for tracking resource usage and managing billing in the Keboola Platform.

## OpenAPI Specification

Available at: https://billing.north-europe.azure.keboola.com/docs/swagger.yaml

## Authentication

The API uses different authentication tokens based on the endpoint:
- `X-StorageApi-Token`: For credits-related operations
- `X-KBC-ManageApiToken` with scope "billing:manage": For duration recording
- `X-KBC-ManageApiToken` with scope "marketplace-subscriptions:manage": For marketplace operations

## Key Features

- Credits balance management
- Workspace duration tracking
- Job duration recording
- Container sandbox monitoring
- Marketplace subscription handling

## Related Concepts

- [Data Integration](../concepts/data-integration.md)
- [Data Transformation](../concepts/data-transformation.md)
- [Scalability](../concepts/scalability.md)

## Related APIs

- [Queue API](queue-api.md)
- [Manage API](manage-api.md)

## Support

For assistance, contact support@keboola.com