# Organization

An Organization in Keboola is a logical grouping of projects. Each organization is assigned to a maintainer (either Keboola or a Keboola Partner) who is responsible for L1 support unless agreed otherwise.

## Overview

Organizations form a hierarchical structure in Keboola:
1. Maintainer (Keboola or Partner)
2. Organization
3. Projects

While projects, organizations, and maintainers form a hierarchy, their users do not. Each unit has an independent set of users:
- Organization users are not automatically users of the projects within organizations
- Project users are not automatically users of the organization
- Each user needs to be explicitly granted access at each level

## Organization Members

All organization members have full privileges and can:
- Add and remove other members
- Leave the organization (requires new invitation to rejoin)
- Access all existing projects in the organization
- View and edit billing details
- Manage shared buckets
- Create new projects
- Change organization settings
- Allow Keboola Support to join projects

## Project Management

Organization members can:
- Create new projects
- Select project templates during creation
- Manage project settings
- Control project access

## Security Features

### Multi-Factor Authentication (MFA)

Organizations can enforce MFA for all projects:
- When enabled, affects all projects in the organization
- Users without MFA can log in to Keboola but cannot access organization projects
- Organization administrators can view users without MFA

### Support Access Control

Organizations can control Keboola Support access:
- Auto Join feature can be enabled/disabled
- When disabled, explicit approval is required for support access
- Settings apply organization-wide

## Related Concepts
- [Project](./project.md)
- [Maintainer](./maintainer.md)
- [Stack](./stack.md)
- [Bucket](./bucket.md)

## Related APIs
- [Manage API](../apis/manage-api.md)
