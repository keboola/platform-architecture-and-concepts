# Artifact

An artifact is a unique set of files associated with a successful job, component, and configuration. Artifacts provide a way for components to produce and consume additional files beyond their primary data processing functionality.

## Purpose

Artifacts allow components to generate and share supplementary outputs such as:
- AI models
- Performance graphs
- Status updates from long-running tasks
- Documentation
- Data quality checks

## Storage

Artifacts are stored in Keboola File Storage with a size limit of 1GB per job.

## Types

There are three types of artifacts:

1. **Runs** - Artifacts from previous runs of the same configuration
2. **Custom** - Artifacts from previous runs of a different configuration
3. **Shared** - Artifacts shared within an orchestration

## File Structure

### Producer
Components can produce artifacts by storing files in:
- `/data/artifacts/out/current` - for runs/custom artifacts
- `/data/artifacts/out/shared` - for shared artifacts within an orchestration

### Consumer
Components can consume artifacts from:
- `/data/artifacts/in/runs/jobs/job-%job_id%` - for runs artifacts
- `/data/artifacts/in/custom/jobs/job-%job_id%` - for custom artifacts
- `/data/artifacts/in/shared/jobs/job-%job_id%` - for shared artifacts

## Configuration

Artifact configuration is specified in the component configuration:

```json
{
  "parameters": {},
  "artifacts": {
    "runs": {
      "enabled": true,
      "filter": {
        "date_since": "-7 days",
        "limit": 5
      }
    },
    "custom": {
      "enabled": true,
      "filter": {
        "component_id": "keboola.python-transformation",
        "config_id": "12345",
        "branch_id": "default",
        "date_since": "-7 days",
        "limit": 5
      }
    },
    "orchestration": {
      "enabled": true
    }
  }
}
```

## Lifecycle

1. Job runner checks for artifacts feature enablement
2. Downloads configured artifacts to appropriate directories
3. Component processes/produces artifacts
4. Job runner compresses artifacts
5. Uploads to File Storage with relevant tags (jobId, componentId, configId, runId, branchId)

## Related Concepts
- [Jobs](./job.md)
- [Components](./component.md)
- [File Storage](./file-storage.md)
