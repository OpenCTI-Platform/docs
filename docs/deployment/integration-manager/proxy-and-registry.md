# Private Registry & Proxy Support

## Overview

XTM Composer supports the deployment of containers from both public and private Docker registries.  
Registry authentication and proxy usage are configured through the OpenCTI daemon settings and automatically applied by the Integration Manager during connector deployment.

This page explains how to configure:
- Private Docker registry authentication
- Registry prefix resolution
- HTTP/HTTPS proxy usage for outbound network calls

## Private Docker Registry

The Integration Manager automatically uses the registry configuration defined under `opencti.daemon.registry`.  
No additional configuration is required inside Composer.

### Configuration through YAML

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
## Proxy Support

XTM Composer can use system proxy settings for outgoing network calls.

### YAML configuration

```yaml
opencti:
  daemon:
    with_proxy: true
```

### Environment variable configuration

```bash
export OPENCTI__DAEMON__WITH_PROXY="true"
export HTTP_PROXY="http://proxy.example.com:8080"
export HTTPS_PROXY="http://proxy.example.com:8080"
export NO_PROXY="localhost,127.0.0.1,.example.com"
```

When enabled, the Integration Manager automatically applies the proxy settings to:

- Docker API calls
- Kubernetes image pulls
- Portainer API requests

## HTTPS Proxy Certificate Support (optional)

Some environments use HTTPS proxies with TLS interception (for example, corporate proxies or debugging proxies like Burp).
In these cases, additional certificate settings may be required.

### Environment variables

```bash
export HTTPS_PROXY_CA='["/path/to/proxy-ca.pem"]'
export HTTPS_PROXY_REJECT_UNAUTHORIZED="false"
```
- HTTPS_PROXY_CA — List of CA certificates (file paths or PEM blocks) used to validate the proxy’s certificate.
- HTTPS_PROXY_REJECT_UNAUTHORIZED — If set to "false", certificate validation is disabled for proxy connections (default behavior).

### Important: Certificate Scope Clarification

Composer distinguishes two independent certificate configurations:

| Purpose                           | Keys                                                  | Description                                                      |
|-----------------------------------|-------------------------------------------------------|------------------------------------------------------------------|
| OpenCTI HTTPS server certificates | app.https_cert.ca, app.https_cert.reject_unauthorized | TLS configuration for the OpenCTI web server                     |
|      Proxy HTTPS certificates     | https_proxy_ca, https_proxy_reject_unauthorized       | Validation settings for HTTPS connections made through the proxy |

These settings must not be mixed.

### Proxy Configuration in config.json

Example of equivalent configuration in a JSON file:

```json
{
  "http_proxy": "http://proxy.example.com:8080",
  "https_proxy": "http://proxy.example.com:8080",
  "no_proxy": "localhost,127.0.0.1,internal.domain",
  "https_proxy_ca": ["/path/to/proxy-ca.pem"],
  "https_proxy_reject_unauthorized": false
}
```
