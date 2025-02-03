# Configuration

A Configuration in Keboola is a specialized concept that represents a complete description of how a component should operate. It is a structured way to store component settings and parameters that describes its behavior.

## Key Aspects

1. **Component Settings**
   - Defines how a specific component should operate
   - Contains all necessary parameters for component execution
   - Stores input and output mapping

2. **Storage**
   - Configurations are stored in the project metadata
   - Can be versioned and backed up
   - Can be shared between projects

3. **Structure**
   - JSON-based format
   - Contains component-specific parameters
   - Includes input/output mapping
   - May contain runtime parameters

4. **Management**
   - Can be created and modified via Storage API
   - Supports versioning
   - Can be exported/imported between projects
   - Supports configuration rows for batch processing

## Configuration File Structure

The configuration file is stored in JSON format and consists of several root nodes:

1. **Storage**
   - Contains input and output mapping for files and tables
   - Used primarily for dynamic input/output mapping
   - Optional for components with static mapping

2. **Parameters**
   - Contains arbitrary parameters passed from the UI to the component
   - Should be validated by the component
   - Can contain encrypted sensitive data
   - Not available in Transformations

3. **Image Parameters**
   - Contains configuration options same for every configuration of a component
   - Cannot be modified by end-users
   - Typically used for global component parameters (tokens, URLs, API versions)
   - Can be different for different Keboola stacks

4. **Authorization**
   - Contains OAuth2 authorization contents
   - Stores workspace credentials

5. **Action**
   - Specifies the name of the action to execute
   - Defaults to 'run'
   - Non-run actions have 30-second execution time limit

## Validation

Components should implement validation for:

1. **Parameters Section**
   - Must validate all parameters passed from UI
   - Should check for required fields
   - Validate data types and formats

2. **Storage Section**
   - Optional validation for specific requirements
   - Check number of tables if required
   - Validate table/column names if needed
   - Must be forward compatible for future features

## Usage

Configurations are used to:
- Define component behavior
- Store connection credentials
- Set up data mappings
- Configure processing parameters
- Enable component orchestration

## Best Practices

1. Use descriptive names for configurations
2. Keep sensitive data in secure parameters
3. Version control important configurations
4. Document configuration changes
5. Use configuration rows for batch processing when applicable

## Related Concepts
- [Component](./component.md)
- [Job](./job.md)
- [Storage Jobs](./storage-jobs.md)
- [Development Branch](./development-branch.md)
- [Workspace](./workspace.md)
