# Storage API Python Client

The Storage API Python Client (`kbcstorage`) is a Python library that provides programmatic access to Keboola Storage API.

## Overview

The Storage API Python Client enables developers to interact with Keboola Storage API using Python. It provides methods for managing buckets, tables, and workspaces, as well as data import and export operations.

## Installation

```bash
pip3 install kbcstorage
```

Or install directly from GitHub:

```bash
pip3 install git+https://github.com/keboola/sapi-python-client.git
```

## Key Features

- Bucket management
- Table operations
- File uploads and downloads
- Data import and export
- Metadata management
- Workspace handling

## Usage Examples

### Client Initialization
```python
from kbcstorage.client import Client

client = Client('https://connection.keboola.com', 'your-token')
```

### Table Operations
```python
# Get table data into local file
client.tables.export_to_file(table_id='in.c-demo.some-table', path_name='/data/')

# Save data
client.tables.create(name='some-table-2', bucket_id='in.c-demo', file_path='/data/some-table')
```

### Bucket Operations
```python
# List buckets
client.buckets.list()

# List bucket tables
client.buckets.list_tables('in.c-demo')
```

## Related Concepts

- [Data Integration](../concepts/data-integration.md)
- [Data Transformation](../concepts/data-transformation.md)
- [Bucket](../concepts/bucket.md)
- [Table Alias](../concepts/table-alias.md)

## Related APIs

- [Storage API](../apis/storage-api.md) 