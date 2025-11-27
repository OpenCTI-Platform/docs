# Private Registry

## Overview

XTM Composer supports the deployment of containers from both public and private Docker registries.  
Registry authentication is configured through the OpenCTI daemon settings and automatically applied by the Integration Manager during connector deployment.

This page explains how to configure:

- Private Docker registry authentication
- Registry prefix resolution

---

## Private Docker Registry

The Integration Manager automatically uses the registry configuration defined under `opencti.daemon.registry`.  
No additional configuration is required inside Composer.

```yaml
opencti:
  daemon:
    registry:
      server: "registry.example.com"
      username: "myuser"
      password: "mypassword"
      email: "user@example.com"
      token_ttl: 1800        # Optional, default 30 minutes
      retry_attempts: 3      # Optional
      retry_delay: 5         # Optional (seconds)
```

## Environment variable configuration

To configure your private registry using environment variables, export:

```bash
export OPENCTI__DAEMON__REGISTRY__SERVER="custom.registry.io"
export OPENCTI__DAEMON__REGISTRY__USERNAME="myuser"
export OPENCTI__DAEMON__REGISTRY__PASSWORD="mypassword"
export OPENCTI__DAEMON__REGISTRY__EMAIL="user@company.com"
export OPENCTI__DAEMON__REGISTRY__TOKEN_TTL="1800"
export OPENCTI__DAEMON__REGISTRY__RETRY_ATTEMPTS="3"
export OPENCTI__DAEMON__REGISTRY__RETRY_DELAY="5"
```

If no registry configuration is provided, the Integration Manager assumes that images are publicly accessible.

## Registry Prefix Resolution

The Integration Manager automatically handles registry prefixes in image names:

- If the image name already includes the registry, it will not prepend anything.
- If no registry is included, the `server` from the registry configuration is automatically prefixed.
- This prevents double-prefixing and ensures images are pulled from the correct registry.

Example:

```yaml
# Image without prefix
image: "opencti/connector-example:1.0.0"

# After resolution
image: "registry.example.com/opencti/connector-example:1.0.0"
```

See also: [Proxy Support](proxy-configuration.md)