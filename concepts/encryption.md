# Encryption

Encryption in Keboola is a security mechanism that ensures sensitive data remains protected throughout the platform. It is primarily used to secure configuration values that contain sensitive information like passwords, tokens, and API keys.

## Overview

The encryption system in Keboola is designed with these key principles:
- One-way encryption (no decryption API available)
- Region-specific encryption
- Component-level access control
- Automatic handling in UI and API

## How It Works

### Encryption Process
1. Values marked for encryption (prefixed with `#`) are automatically encrypted using the Encryption API
2. Encrypted values are stored securely and can only be decrypted within components
3. Decryption happens during configuration serialization to Docker containers
4. Decrypted data is temporarily stored on Docker host and deleted after container completion

### Encryption Types

Based on provided parameters, different types of encryption ciphers are created:

| Parameters | Cipher Prefix | Scope |
|------------|--------------|--------|
| componentId | `KBC::ComponentSecure::` | All configurations of the component |
| componentId + projectId | `KBC::ProjectSecure::` | Project's component configurations |
| componentId + projectId + configId | `KBC::ConfigSecure::` | Specific configuration only |
| projectId | `KBC::ProjectWideSecure::` | All project configurations |

Additional branch-specific ciphers are available when using the `branchType` parameter:
- `KBC::BranchTypeSecure::`
- `KBC::BranchTypeConfigSecure::`
- `KBC::ProjectWideBranchTypeSecure::`

## Usage

### In Configuration
To encrypt a value in configuration, prefix its key with `#`:

```json
{
    "username": "JohnDoe",
    "#password": "secret"
}
```

After encryption:
```json
{
    "username": "JohnDoe",
    "#password": "KBC::ProjectSecure::ENCODEDSTRING"
}
```

## Important Notes

- Encrypted values are region-specific and cannot be transferred between regions
- No decryption API is available for security reasons
- Re-encrypting an already encrypted value has no effect
- Configuration copying may fail if it contains `KBC::ConfigSecure::` ciphers

## Related Concepts

- [Configuration](./configuration.md)
- [Component](./component.md)

## Related APIs

- [Encryption API](../apis/encryption-api.md)
- [Storage API](../apis/storage-api.md)
