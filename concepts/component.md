# Component

A Component in Keboola is a modular building block that allows you to extend the platform's functionality. Components exchange data through CSV files in designated directories and run in isolated Docker environments.

## Overview

Components are the building blocks for extending Keboola functionality. Each component must adhere to a common interface that specifies how CSV files and designated folders are used to exchange data between Keboola and the component.

The main data exchange patterns are:
- Applications process input tables from CSV files and generate result tables in CSV files
- Extractors write results like applications but get input from external sources
- Writers read input tables like applications but push results to external systems

## Component Types

The following component types are currently supported:

1. **Extractor**
   - Brings data into Keboola Storage
   - Uses no Storage tables on input
   - Has a user interface
   - Primary purpose is data extraction from external systems

2. **Writer**
   - Loads data from Storage into external systems
   - Generates no Storage tables on output
   - Has a user interface
   - Primary purpose is data export to external systems

3. **Application**
   - Processes data in Storage
   - Can work as wrapped transformation
   - May use external services
   - Has a user interface

4. **Processor**
   - Designed for post-processing or pre-processing data
   - Not designed to run standalone
   - Has no user interface

5. **Code Pattern**
   - Generates code for transformations
   - Has a user interface

## Common Interface Features

The common interface provides extensive functionality:
- Parameter passing
- Error control
- Metadata management
- OAuth support
- Non-CSV file handling
- Logging configuration

## Implementation Requirements

To develop a new component, you need:
1. A Keboola project for testing
2. Docker knowledge and ability to run docker commands
3. API request capabilities (Postman recommended)
4. Git repository (Github or Bitbucket recommended)

## Related Concepts
- [Configuration](./configuration.md)
- [Job](./job.md)
- [Docker Runner](./docker-runner.md)
- [Common Interface](./common-interface.md)

## OpenAPI Specification
For detailed API documentation, see the [Components API specification](https://app.swaggerhub.com/apis-docs/keboola/components/) 