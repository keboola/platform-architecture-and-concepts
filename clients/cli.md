# Keboola CLI

The Keboola Command Line Interface (CLI) is a powerful tool that allows you to interact with the Keboola platform directly from your terminal. It provides functionality for managing projects, components, configurations, and other Keboola resources.

## Installation

### Using Homebrew (macOS)
```bash
brew install keboola/tap/keboola-cli
```

### Manual Installation
Download the latest release from the [GitHub releases page](https://github.com/keboola/keboola-as-code/releases).

## Authentication

To use the CLI, you need to authenticate with your Keboola project. You can do this using a [storage token](../concepts/token.md):

```bash
kbc auth login --token your-storage-token
```

## Basic Usage

The CLI follows a consistent command structure:
```bash
kbc [command] [subcommand] [options]
```

### Common Commands

#### Project Management
- `kbc project init` - Initialize a new project
- `kbc project select` - Select an existing project
- `kbc project status` - Show project status

#### Configuration Management
- `kbc create component` - Create a new [component](../concepts/component.md) configuration
- `kbc create config` - Create a new [configuration](../concepts/configuration.md)
- `kbc sync pull` - Pull configurations from Keboola
- `kbc sync push` - Push configurations to Keboola

#### Job Operations
- `kbc job run` - Run a [job](../concepts/job.md)
- `kbc job status` - Check job status
- `kbc job list` - List recent jobs

#### Storage Operations
- `kbc storage bucket list` - List [buckets](../concepts/bucket.md)
- `kbc storage table list` - List [tables](../concepts/table.md)
- `kbc storage file list` - List files in [file storage](../concepts/file-storage.md)

## Working with Templates

The CLI supports working with templates for automation and standardization:

```bash
kbc template create
kbc template use
kbc template list
```

## Development Mode

For developers, the CLI provides a development mode with additional features:

```bash
kbc dev branch create    # Create a [development branch](../concepts/development-branch.md)
kbc dev branch list      # List development branches
kbc dev workspace create # Create a [workspace](../concepts/workspace.md)
```

## Related Concepts

- [Component](../concepts/component.md)
- [Configuration](../concepts/configuration.md)
- [Job](../concepts/job.md)
- [Storage Job](../concepts/storage-job.md)
- [Bucket](../concepts/bucket.md)
- [Table](../concepts/table.md)
- [File Storage](../concepts/file-storage.md)
- [Token](../concepts/token.md)
- [Development Branch](../concepts/development-branch.md)
- [Workspace](../concepts/workspace.md)

## Related APIs

- [Storage API](../apis/storage-api.md)
- [Manage API](../apis/manage-api.md)

For detailed information about specific commands and options, use:
```bash
kbc --help
kbc [command] --help
```
