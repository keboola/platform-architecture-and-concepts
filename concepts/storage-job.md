# Storage Job

A Storage Job in Keboola is a unique concept that represents and manages long-running asynchronous data operations within the platform. It provides a standardized way to handle, monitor, and control potentially time-consuming tasks.

Jobs are objects that manage asynchronous tasks, these are all potentially long-running actions such as loading table data, snapshotting, table structure modifications. Jobs are created by actions on target resources, e.g., an Asynchronous import request creates a table import job. For each new job, the Id and URL of the job is returned.

When working with jobs, follow these steps:
1. Start the job - Job resource is created and a URL for polling is returned
2. Poll job status - Poll the job URL in a loop until the job status is set to success or error
3. Check the results - When the job is finished, the results field will contain the actual result of the original request

Available Statuses:
- **waiting** - The job is in the queue and is waiting for execution
- **processing** - The job is being processed by a worker
- **success** - The job is done with a success
- **error** - The job is done with an error

## Key Characteristics

1. **Operation Management**
   - Handles long-running tasks
   - Provides status tracking
   - Manages resource allocation
   - Controls execution flow

2. **Status Tracking**
   - Waiting state
   - Processing state
   - Success/Error states
   - Progress monitoring
   - Result handling

3. **Resource Types**
   - Table operations
   - Data loading/unloading
   - Bucket operations
   - Workspace operations
   - Transformation executions

4. **Metadata**
   - Creation time
   - Start/End times
   - Creator information
   - Resource metrics
   - Operation parameters

## Usage Patterns

1. **Asynchronous Operations**
   - Table data loading
   - Snapshotting
   - Data export
   - Structure modifications
   - Workspace preparation

2. **Monitoring and Control**
   - Status polling
   - Result retrieval
   - Error handling
   - Resource cleanup

## Best Practices

1. Implement proper polling intervals
2. Handle all possible job states
3. Implement timeout mechanisms
4. Log job progress
5. Monitor job metrics
6. Clean up completed jobs
7. Document job purposes 

## Related Concepts
- [Job](./job.md)
- [Component](./component.md)
- [Configuration](./configuration.md)
- [Table](./table.md)
- [Event](./event.md) 