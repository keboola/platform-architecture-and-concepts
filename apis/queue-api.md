# Queue API

The Queue API manages asynchronous job execution in Keboola Platform.

## Overview

The Queue API is responsible for managing and monitoring asynchronous job execution across the platform. It provides functionality for job scheduling, status monitoring, result retrieval, and error handling.

## OpenAPI specification

Available at: https://queue.north-europe.azure.keboola.com/docs/swagger.yaml

## Authentication

The API uses the Storage API token for authentication:
- `X-StorageApi-Token`: Required for all operations

## Related Concepts

- [Job](../concepts/job.md)
- [Component](../concepts/component.md)
- [Configuration](../concepts/configuration.md)
- [Event](../concepts/event.md)

## Related APIs

- [Storage API](storage-api.md)
- [Transformation API](transformation-api.md)

## Services

The Queue API is managed by the following services:
- Job Queue Service
- Job Runner Service 