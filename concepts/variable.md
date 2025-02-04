# Variable

Variables in Keboola are placeholders used in configurations that get resolved at job runtime. They enable dynamic configuration management and code reuse across different components.

## Overview

Variables use Moustache template syntax (`{{ variableName }}`) and can be placed anywhere in the JSON configuration body. They cannot be used in configuration names or descriptions.

To work with variables, three elements are required:
1. **Main Configuration** - The configuration where variables are used (can be any component configuration)
2. **Variable Configuration** - A configuration of the `keboola.variables` component that defines the variables
3. **Variable Values** - The actual values to be used during runtime

## Variable Configuration

Variable configuration is a special configuration tied to the `keboola.variables` component. It defines the names and types of variables that can be used. Example:

```json
{
    "variables": [
        {
            "name": "firstVariable",
            "type": "string"
        },
        {
            "name": "secondVariable",
            "type": "string"
        }
    ]
}
```

Note: Currently, all variables are of type `string`.

## Variable Values

Variable values can be provided in several ways:
1. Default values in the variable configuration
2. Stored values in configuration rows
3. Runtime values when executing a job
4. Values specified in orchestration tasks

## Evaluation Sequence

Variables are evaluated in the following order (later sources override earlier ones):
1. Default values from variable configuration
2. Values stored in component configuration
3. Values stored in orchestration task settings
4. Values provided in job parameters

## Shared Code

Related to variables is the Shared Code feature, which allows sharing configuration code parts. Key characteristics:
- Uses the same Moustache syntax for replacement
- Referenced using `shared_code_id` and `shared_code_row_ids`
- Cannot be overridden at runtime
- Can contain its own variables
- Must be referenced within an array in the configuration JSON

## Usage Restrictions

Variables cannot be used in:
- Legacy transformations (component ID: `transformation`)
- Configuration names or descriptions
- Non-array elements when using shared code

## Related Concepts
- [Configuration](./configuration.md)
- [Component](./component.md)
- [Job](./job.md)
- [Orchestration](./orchestration.md)

## Related APIs
- [Storage API](../apis/storage-api.md)
- [Queue API](../apis/queue-api.md)
