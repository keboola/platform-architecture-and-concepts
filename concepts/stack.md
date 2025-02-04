# Stack

A Stack in Keboola represents an independent, full instance of Keboola platform services, combining a specific datacenter location (region) and cloud provider.

## Overview

Each stack is identified by its unique domain (URL) and represents a complete, isolated instance of the Keboola platform. Projects in different stacks require separate Keboola accounts, as stacks operate independently.

## Types

### Multi-tenant Stacks

Current multi-tenant stacks:

| Location | Cloud Provider | Domain |
|----------|---------------|---------|
| US Virginia | AWS | connection.keboola.com |
| US Virginia | GCP | connection.us-east4.gcp.keboola.com |
| EU Frankfurt | AWS | connection.eu-central-1.keboola.com |
| EU Ireland | Azure | connection.north-europe.azure.keboola.com |
| EU Frankfurt | GCP | connection.europe-west3.gcp.keboola.com |

### Single-tenant Stacks

Single-tenant stacks are available for enterprise customers.

## Key Characteristics

- Each stack is a complete, independent instance
- Separate networks with dedicated IP addresses
- Requires separate accounts for different stacks
- Supports multiple cloud providers (AWS, Azure, GCP)
- Region-specific deployments

## Related Concepts

- [Data Integration](../features/data-integration.md)
- [Data Governance](../features/data-governance.md)
- [Configuration](configuration.md)
- [Project](project.md)

## Related APIs

- [Storage API](../apis/storage-api.md)
- [Manage API](../apis/manage-api.md)