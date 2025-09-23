# Quick Start Guide

This guide will help you get XTM Composer up and running quickly with OpenCTI.

## Prerequisites

Before starting, ensure you have:
- XTM Composer installed (see [Installation Guide](installation.md))
- Access to an OpenCTI instance
- OpenCTI API token
- RSA private key (4096-bit)

## Step 1: Generate RSA Private Key

Generate a 4096-bit RSA private key for authentication:

```bash
openssl genrsa -out private_key_4096.pem 4096
```

## Step 2: Basic Configuration

Create a configuration file based on your environment.

### Option A: Using Configuration File

Create `config/production.yaml`:

```yaml
manager:
  id: "my-manager-001"
  name: "Production Manager"
  credentials_key_filepath: "/path/to/private_key_4096.pem"
  logger:
    level: info
    format: json

opencti:
  enable: true
  url: "https://opencti.example.com"
  token: "your-opencti-api-token"
  daemon:
    selector: kubernetes  # or 'docker' or 'portainer'
```

### Option B: Using Environment Variables

Set configuration through environment variables:

```bash
export COMPOSER_ENV=production
export MANAGER__ID="my-manager-001"
export MANAGER__CREDENTIALS_KEY_FILEPATH="/path/to/private_key_4096.pem"
export OPENCTI__URL="https://opencti.example.com"
export OPENCTI__TOKEN="your-opencti-api-token"
export OPENCTI__DAEMON__SELECTOR="kubernetes"
```

## Step 3: Choose Your Orchestration Platform

### For Kubernetes

```yaml
opencti:
  daemon:
    selector: kubernetes
    kubernetes:
      image_pull_policy: IfNotPresent
```

### For Docker

```yaml
opencti:
  daemon:
    selector: docker
    docker:
      network_mode: bridge
```

**Note**: Docker mode requires socket access:
```bash
docker run -v /var/run/docker.sock:/var/run/docker.sock ...
```

### For Portainer

```yaml
opencti:
  daemon:
    selector: portainer
    portainer:
      api: "https://portainer.example.com:9443"
      api_key: "your-portainer-api-key"
      env_id: "3"
      env_type: "docker"
```

## Step 4: Run XTM Composer

### Using Docker

```bash
docker run -d \
  --name xtm-composer \
  -v $(pwd)/config:/config \
  -v $(pwd)/private_key_4096.pem:/keys/private_key.pem \
  -e COMPOSER_ENV=production \
  filigran/xtm-composer:latest
```

### Using Binary

```bash
COMPOSER_ENV=production ./xtm-composer
```

## Step 5: Verify Connection

Check the logs to verify XTM Composer is connected to OpenCTI:

```bash
# Docker
docker logs xtm-composer

# Binary/Systemd
tail -f /var/log/xtm-composer/composer.log
```

You should see messages like:
```
INFO  Starting XTM Composer
INFO  Connecting to OpenCTI at https://opencti.example.com
INFO  Successfully connected to OpenCTI
INFO  Manager registered with ID: my-manager-001
```

## Step 6: Verify in OpenCTI

1. Log into your OpenCTI instance
2. Navigate to **Data > Connectors**
3. You should see your XTM Composer manager listed
4. Connectors managed by XTM Composer will show the manager ID

## Common Configuration Examples

### Development Environment

```yaml
manager:
  id: "dev-manager"
  credentials_key_filepath: "./private_key_4096.pem"
  logger:
    level: debug
    format: pretty
    console: true
  debug:
    show_env_vars: true

opencti:
  enable: true
  url: "http://localhost:4000"
  token: "development-token"
  daemon:
    selector: docker
```

### Production with High Availability

```yaml
manager:
  id: "prod-manager-ha"
  execute_schedule: 5      # Check every 5 seconds
  ping_alive_schedule: 30  # Ping every 30 seconds
  logger:
    level: warn
    format: json
    directory: true
    console: false

opencti:
  enable: true
  url: "https://opencti.prod.example.com"
  token: "${OPENCTI_TOKEN}"  # Use environment variable
  logs_schedule: 5
  daemon:
    selector: kubernetes
    kubernetes:
      image_pull_policy: Always
```

## Troubleshooting

### Connection Issues

If XTM Composer cannot connect to OpenCTI:

1. **Verify URL and Token**:
   ```bash
   curl -H "Authorization: Bearer YOUR_TOKEN" https://opencti.example.com/graphql
   ```

2. **Check Network Connectivity**:
   ```bash
   ping opencti.example.com
   nc -zv opencti.example.com 443
   ```

3. **SSL Certificate Issues**:
   Set `unsecured_certificate: true` for self-signed certificates (not recommended for production)

### Authentication Failures

1. **Verify RSA Key**:
   ```bash
   openssl rsa -in private_key_4096.pem -check
   ```

2. **Check File Permissions**:
   ```bash
   chmod 600 private_key_4096.pem
   ```

3. **Verify Key Path**:
   Ensure the path in configuration matches the actual key location

### Orchestration Issues

**Kubernetes**: Verify cluster access:
```bash
kubectl cluster-info
kubectl auth can-i create deployments
```

**Docker**: Check socket permissions:
```bash
docker info
ls -la /var/run/docker.sock
```

**Portainer**: Test API access:
```bash
curl -H "X-API-Key: YOUR_KEY" https://portainer.example.com/api/endpoints
```

## Next Steps

- Review the complete [Configuration Reference](configuration.md)
- Set up monitoring and alerting
- Configure connector-specific settings
- Implement security best practices
- Join the OpenCTI community for support
