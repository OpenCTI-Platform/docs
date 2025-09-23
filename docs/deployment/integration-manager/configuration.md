# Configuration Reference

XTM Composer uses a layered configuration system with support for YAML files and environment variables. Environment variables override file-based configuration.

## Configuration Priority

1. Environment variables (highest priority)
2. Environment-specific config file (e.g., `production.yaml`)
3. Default config file (`default.yaml`)

## Environment Variable Format

All environment variables use double underscores (`__`) to separate nested configuration levels.

Example: `manager.logger.level` becomes `MANAGER__LOGGER__LEVEL`

## Configuration Sections

### 1. Manager Configuration

Core settings for the XTM Composer manager instance.

#### manager.id
- **YAML Path**: `manager.id`
- **Environment Variable**: `MANAGER__ID`
- **Description**: Unique identifier for this manager instance
- **Required**: Yes
- **Type**: string
- **Default**: `default-manager-id`

#### manager.name
- **YAML Path**: `manager.name`
- **Environment Variable**: `MANAGER__NAME`
- **Description**: Human-readable name for the manager
- **Required**: No
- **Type**: string
- **Default**: `Filigran connector manager`

#### manager.execute_schedule
- **YAML Path**: `manager.execute_schedule`
- **Environment Variable**: `MANAGER__EXECUTE_SCHEDULE`
- **Description**: Interval in seconds between execution cycles
- **Required**: No
- **Type**: integer
- **Default**: `10`

#### manager.ping_alive_schedule
- **YAML Path**: `manager.ping_alive_schedule`
- **Environment Variable**: `MANAGER__PING_ALIVE_SCHEDULE`
- **Description**: Interval in seconds between alive ping messages
- **Required**: No
- **Type**: integer
- **Default**: `60`

#### manager.credentials_key
- **YAML Path**: `manager.credentials_key`
- **Environment Variable**: `MANAGER__CREDENTIALS_KEY`
- **Description**: RSA private key content (4096-bit recommended). Use for direct key embedding
- **Required**: One of `credentials_key` or `credentials_key_filepath` is required
- **Type**: string (multiline)
- **Default**: None

#### manager.credentials_key_filepath
- **YAML Path**: `manager.credentials_key_filepath`
- **Environment Variable**: `MANAGER__CREDENTIALS_KEY_FILEPATH`
- **Description**: Path to RSA private key file. Takes priority over `credentials_key` if both are set
- **Required**: One of `credentials_key` or `credentials_key_filepath` is required
- **Type**: string
- **Default**: None

### 2. Logging Configuration

#### manager.logger.level
- **YAML Path**: `manager.logger.level`
- **Environment Variable**: `MANAGER__LOGGER__LEVEL`
- **Description**: Logging verbosity level
- **Required**: No
- **Type**: string
- **Possible Values**: `trace`, `debug`, `info`, `warn`, `error`
- **Default**: `info`

#### manager.logger.format
- **YAML Path**: `manager.logger.format`
- **Environment Variable**: `MANAGER__LOGGER__FORMAT`
- **Description**: Log output format
- **Required**: No
- **Type**: string
- **Possible Values**: `json`, `pretty`
- **Default**: `json`

#### manager.logger.directory
- **YAML Path**: `manager.logger.directory`
- **Environment Variable**: `MANAGER__LOGGER__DIRECTORY`
- **Description**: Enable logging to directory/file
- **Required**: No
- **Type**: boolean
- **Default**: `true`

#### manager.logger.console
- **YAML Path**: `manager.logger.console`
- **Environment Variable**: `MANAGER__LOGGER__CONSOLE`
- **Description**: Enable logging to console/stdout
- **Required**: No
- **Type**: boolean
- **Default**: `true`

### 3. Debug Configuration

#### manager.debug.show_env_vars
- **YAML Path**: `manager.debug.show_env_vars`
- **Environment Variable**: `MANAGER__DEBUG__SHOW_ENV_VARS`
- **Description**: Display environment variables at startup (excludes sensitive data)
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### manager.debug.show_sensitive_env_vars
- **YAML Path**: `manager.debug.show_sensitive_env_vars`
- **Environment Variable**: `MANAGER__DEBUG__SHOW_SENSITIVE_ENV_VARS`
- **Description**: Display sensitive environment variables at startup (tokens, keys, etc.)
- **Required**: No
- **Type**: boolean
- **Default**: `false`

### 4. OpenCTI Configuration

Settings for OpenCTI platform integration.

#### opencti.enable
- **YAML Path**: `opencti.enable`
- **Environment Variable**: `OPENCTI__ENABLE`
- **Description**: Enable OpenCTI integration
- **Required**: No
- **Type**: boolean
- **Default**: `true`

#### opencti.url
- **YAML Path**: `opencti.url`
- **Environment Variable**: `OPENCTI__URL`
- **Description**: OpenCTI platform URL
- **Required**: Yes (if OpenCTI is enabled)
- **Type**: string
- **Default**: `http://host.docker.internal:4000`

#### opencti.token
- **YAML Path**: `opencti.token`
- **Environment Variable**: `OPENCTI__TOKEN`
- **Description**: OpenCTI API authentication token
- **Required**: Yes (if OpenCTI is enabled)
- **Type**: string
- **Default**: `ChangeMe`

#### opencti.unsecured_certificate
- **YAML Path**: `opencti.unsecured_certificate`
- **Environment Variable**: `OPENCTI__UNSECURED_CERTIFICATE`
- **Description**: Allow self-signed SSL certificates
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### opencti.with_proxy
- **YAML Path**: `opencti.with_proxy`
- **Environment Variable**: `OPENCTI__WITH_PROXY`
- **Description**: Use system proxy settings for connection
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### opencti.logs_schedule
- **YAML Path**: `opencti.logs_schedule`
- **Environment Variable**: `OPENCTI__LOGS_SCHEDULE`
- **Description**: Maximum interval in seconds between log reports
- **Required**: No
- **Type**: integer
- **Default**: `10`

### 5. OpenAEV Configuration (Coming Soon)

Settings for OpenAEV platform integration. **Note: OpenAEV module is not yet implemented.**

#### openbas.enable
- **YAML Path**: `openbas.enable`
- **Environment Variable**: `OPENBAS__ENABLE`
- **Description**: Enable OpenBAS integration (Coming Soon)
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### openbas.url
- **YAML Path**: `openbas.url`
- **Environment Variable**: `OPENBAS__URL`
- **Description**: OpenBAS platform URL (Coming Soon)
- **Required**: Yes (if OpenBAS is enabled)
- **Type**: string
- **Default**: `http://host.docker.internal:4000`

#### openbas.token
- **YAML Path**: `openbas.token`
- **Environment Variable**: `OPENBAS__TOKEN`
- **Description**: OpenBAS API authentication token (Coming Soon)
- **Required**: Yes (if OpenBAS is enabled)
- **Type**: string
- **Default**: `ChangeMe`

#### openbas.unsecured_certificate
- **YAML Path**: `openbas.unsecured_certificate`
- **Environment Variable**: `OPENBAS__UNSECURED_CERTIFICATE`
- **Description**: Allow self-signed SSL certificates (Coming Soon)
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### openbas.with_proxy
- **YAML Path**: `openbas.with_proxy`
- **Environment Variable**: `OPENBAS__WITH_PROXY`
- **Description**: Use system proxy settings (Coming Soon)
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### openbas.logs_schedule
- **YAML Path**: `openbas.logs_schedule`
- **Environment Variable**: `OPENBAS__LOGS_SCHEDULE`
- **Description**: Log report interval in seconds (Coming Soon)
- **Required**: No
- **Type**: integer
- **Default**: `10`

### 6. Orchestration Settings

#### daemon.selector
- **YAML Path**: `{opencti|openbas}.daemon.selector`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__SELECTOR`
- **Description**: Container orchestration platform to use
- **Required**: Yes
- **Type**: string
- **Possible Values**: `kubernetes`, `docker`, `portainer`
- **Default**: `kubernetes`

### 7. Kubernetes Configuration

Settings specific to Kubernetes orchestration.

#### daemon.kubernetes.image_pull_policy
- **YAML Path**: `{opencti|openbas}.daemon.kubernetes.image_pull_policy`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__KUBERNETES__IMAGE_PULL_POLICY`
- **Description**: Image pull policy for containers created by XTM Composer
- **Required**: No
- **Type**: string
- **Possible Values**: `Always`, `IfNotPresent`, `Never`
- **Default**: `IfNotPresent`

#### daemon.kubernetes.base_deployment
- **YAML Path**: `{opencti|openbas}.daemon.kubernetes.base_deployment`
- **Environment Variable**: Not supported for complex objects
- **Description**: Base Kubernetes Deployment manifest template
- **Required**: No
- **Type**: Kubernetes Deployment object
- **Default**: None

#### daemon.kubernetes.base_deployment_json
- **YAML Path**: `{opencti|openbas}.daemon.kubernetes.base_deployment_json`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__KUBERNETES__BASE_DEPLOYMENT_JSON`
- **Description**: Base Deployment manifest as JSON string
- **Required**: No
- **Type**: JSON string
- **Default**: None

### 8. Docker Configuration

Settings specific to Docker orchestration.

#### daemon.docker.network_mode
- **YAML Path**: `{opencti|openbas}.daemon.docker.network_mode`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__NETWORK_MODE`
- **Description**: Docker network mode for containers
- **Required**: No
- **Type**: string
- **Possible Values**: `bridge`, `host`, `none`, or custom network name
- **Default**: `bridge`

#### daemon.docker.extra_hosts
- **YAML Path**: `{opencti|openbas}.daemon.docker.extra_hosts`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__EXTRA_HOSTS`
- **Description**: Additional hosts entries for containers
- **Required**: No
- **Type**: array of strings
- **Default**: None

#### daemon.docker.dns
- **YAML Path**: `{opencti|openbas}.daemon.docker.dns`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__DNS`
- **Description**: Custom DNS servers for containers
- **Required**: No
- **Type**: array of strings
- **Default**: None

#### daemon.docker.privileged
- **YAML Path**: `{opencti|openbas}.daemon.docker.privileged`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__PRIVILEGED`
- **Description**: Run containers in privileged mode
- **Required**: No
- **Type**: boolean
- **Default**: `false`

#### daemon.docker.cap_add
- **YAML Path**: `{opencti|openbas}.daemon.docker.cap_add`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__CAP_ADD`
- **Description**: Linux capabilities to add
- **Required**: No
- **Type**: array of strings
- **Default**: None

#### daemon.docker.cap_drop
- **YAML Path**: `{opencti|openbas}.daemon.docker.cap_drop`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__CAP_DROP`
- **Description**: Linux capabilities to drop
- **Required**: No
- **Type**: array of strings
- **Default**: None

#### daemon.docker.shm_size
- **YAML Path**: `{opencti|openbas}.daemon.docker.shm_size`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__DOCKER__SHM_SIZE`
- **Description**: Shared memory size in bytes
- **Required**: No
- **Type**: integer
- **Default**: None

### 9. Portainer Configuration

Settings specific to Portainer orchestration.

#### daemon.portainer.api
- **YAML Path**: `{opencti|openbas}.daemon.portainer.api`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__API`
- **Description**: Portainer API endpoint URL
- **Required**: Yes (if using Portainer)
- **Type**: string
- **Default**: `https://host.docker.internal:9443`

#### daemon.portainer.api_key
- **YAML Path**: `{opencti|openbas}.daemon.portainer.api_key`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__API_KEY`
- **Description**: Portainer API authentication key
- **Required**: Yes (if using Portainer)
- **Type**: string
- **Default**: `ChangeMe`

#### daemon.portainer.env_id
- **YAML Path**: `{opencti|openbas}.daemon.portainer.env_id`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__ENV_ID`
- **Description**: Portainer environment ID
- **Required**: Yes (if using Portainer)
- **Type**: string
- **Default**: `3`

#### daemon.portainer.env_type
- **YAML Path**: `{opencti|openbas}.daemon.portainer.env_type`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__ENV_TYPE`
- **Description**: Portainer environment type
- **Required**: Yes (if using Portainer)
- **Type**: string
- **Possible Values**: `docker`, `kubernetes`
- **Default**: `docker`

#### daemon.portainer.api_version
- **YAML Path**: `{opencti|openbas}.daemon.portainer.api_version`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__API_VERSION`
- **Description**: Docker API version for Portainer
- **Required**: No
- **Type**: string
- **Default**: `v1.41`

#### daemon.portainer.stack
- **YAML Path**: `{opencti|openbas}.daemon.portainer.stack`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__STACK`
- **Description**: Portainer stack name for deployment
- **Required**: No
- **Type**: string
- **Default**: None

#### daemon.portainer.network_mode
- **YAML Path**: `{opencti|openbas}.daemon.portainer.network_mode`
- **Environment Variable**: `{OPENCTI|OPENBAS}__DAEMON__PORTAINER__NETWORK_MODE`
- **Description**: Network mode for Portainer-managed containers
- **Required**: No
- **Type**: string
- **Default**: None

## Environment Configuration

### COMPOSER_ENV
- **Environment Variable**: `COMPOSER_ENV`
- **Description**: Specifies which configuration file to load (e.g., `development`, `production`)
- **Required**: No
- **Type**: string
- **Default**: `production`

## Complete Configuration Example

```yaml
# config/production.yaml
manager:
  id: prod-manager-001
  name: Production XTM Manager
  execute_schedule: 10
  ping_alive_schedule: 60
  credentials_key_filepath: /keys/private_key_4096.pem
  logger:
    level: info
    format: json
    directory: true
    console: false

opencti:
  enable: true
  url: https://opencti.example.com
  token: ${OPENCTI_TOKEN}  # Reference env variable
  unsecured_certificate: false
  with_proxy: false
  logs_schedule: 10
  daemon:
    selector: kubernetes
    kubernetes:
      image_pull_policy: IfNotPresent

openbas:
  enable: false  # Coming Soon
```

## Security Best Practices

1. **Never commit credentials**: Use environment variables or secure secret management
2. **Use file-based keys**: Prefer `credentials_key_filepath` over embedding keys
3. **Restrict file permissions**: Set key files to `600` permissions
4. **Rotate tokens regularly**: Update API tokens periodically
5. **Use TLS/SSL**: Always use HTTPS in production
6. **Limit debug output**: Disable `show_sensitive_env_vars` in production
