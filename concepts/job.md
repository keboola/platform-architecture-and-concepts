# Component Job

Component Jobs are asynchronous operations in Keboola that execute various tasks like data extraction or application running in the background. When an operation is triggered, it creates a job that either starts executing immediately or waits in a queue.

## Key Characteristics

- **Asynchronous Execution**: Jobs run in the background and require monitoring for completion
- **Automatic Queuing**: Jobs are automatically queued and executed based on resource availability
- **Component-specific Limits**: Each component has defined limits for execution time and memory consumption

## Job Properties

When a job is created, it contains several key properties:

- **Job ID**: Unique identifier for the job
- **Run ID**: Identifier that can track related jobs
- **Status**: Current state of the job (e.g., created, processing, success)
- **Component**: The component executing the job
- **Configuration**: Associated configuration ID and data
- **Timing**: Creation time, start time, end time, and duration
- **Mode**: Execution mode (run, debug)
- **Type**: Job type classification

## Job Types

There are four main types of jobs:

1. **Standard**: 
   - Performs actual work
   - Consumes billable time
   - Counts towards resource consumption

2. **Container**:
   - Contains parallel executions of configuration rows
   - Virtual container type

3. **Phase Container**:
   - Contains standard jobs in a single orchestration phase
   - Virtual container type

4. **Orchestration Container**:
   - Represents an orchestration
   - Contains phase jobs
   - Virtual container type

## Job Lifecycle

1. **Creation**: Job is created with initial properties
2. **Queuing**: Job waits for available resources if necessary
3. **Execution**: Job processes its assigned task
4. **Completion**: Job reaches a final state (success/error)

## Debug Mode

Debug mode offers special execution properties:

- Creates data folder snapshots at various stages
- Uploads snapshots to project's Files in Storage
- Does not perform output mapping to Storage
- Useful for development and troubleshooting
- Safe to execute as it doesn't modify project data

## Best Practices

1. **Monitoring**: Implement proper job polling to track status
2. **Error Handling**: Account for various job states in your implementation
3. **Resource Management**: Be aware of component-specific limits
4. **Debug Usage**: Use debug mode for development and testing

## Related Concepts

- [Configuration](./configuration.md)
- [Component](./component.md)
- [File Storage](./file-storage.md)
- [Job](./job.md)
