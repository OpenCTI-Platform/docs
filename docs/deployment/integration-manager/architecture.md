# XTM Composer Architecture

## Overview

XTM Composer is a micro-orchestration tool that manages connectors/collectors/injectors for Filigran platforms (OpenCTI and OpenAEV). Written in Rust for performance and reliability, it acts as a bridge between the platforms and container orchestration systems.

## Global Mechanism

### Core Architecture Flow

```
┌─────────────┐     REST/GraphQL     ┌──────────────┐
│   OpenCTI   │◄────────────────────►│              │
│   Platform  │                      │  XTM         │     Container API
└─────────────┘                      │  Composer    │◄─────────────────────►┌─────────────┐
                                     │              │                       │ Orchestrator│
┌─────────────┐                      │              │                       │ (K8s/Docker)│
│   OpenAEV   │◄────────────────────►│              │                       └─────────────┘
│   Platform  │     REST/GraphQL     └──────────────┘
└─────────────┘
```

### Main Components

1. **Engine Module** (`src/engine/`)
   - Manages lifecycle of orchestration and health monitoring
   - Creates separate async tasks for each platform (OpenCTI/OpenAEV)
   - Handles graceful shutdown via system signals

2. **API Module** (`src/api/`)
   - Abstracts platform communication through `ComposerApi` trait
   - Handles GraphQL queries/mutations for OpenCTI
   - Manages connector configuration retrieval and status updates

3. **Orchestrator Module** (`src/orchestrator/`)
   - Implements `Orchestrator` trait for different container systems
   - Supports Kubernetes, Docker, and Portainer
   - Handles container lifecycle operations

4. **Config Module** (`src/config/`)
   - Manages application settings from YAML files
   - Supports environment-specific configurations
   - Handles credentials and encryption keys

### Execution Flow

1. **Initialization**
   - Load configuration from `config/default.yaml` and environment
   - Initialize RSA private key for configuration decryption
   - Set up logging system

2. **Platform Registration**
   - Connect to platform (OpenCTI/OpenAEV)
   - Register composer instance with unique manager ID
   - Verify API compatibility

3. **Main Orchestration Loop**
   - Execute every `execute_schedule` seconds (default: 30s)
   - Pull connector configurations
   - Reconcile desired vs actual state
   - Apply necessary changes

## Pull Mechanism

### Configuration Retrieval

The composer periodically pulls connector configurations from the platform:

```rust
// Executed every 30 seconds by default
async fn orchestrate() {
    // 1. Fetch all connector configurations from platform
    let connectors = api.connectors().await;
    
    // 2. For each connector configuration
    for connector in connectors {
        // 3. Check if container exists in orchestrator
        let container = orchestrator.get(connector).await;
        
        // 4. Reconcile state
        match container {
            Some(c) => orchestrate_existing(c, connector),
            None => orchestrate_missing(connector)
        }
    }
}
```

### Connector Configuration Structure

Each connector configuration contains:
- **ID**: Unique identifier
- **Name**: Human-readable name
- **Image**: Docker image to deploy
- **Contract Hash**: Version identifier for configuration
- **Status**: Current and requested states
- **Configuration**: Key-value pairs (potentially encrypted)

### State Synchronization

The composer maintains two-way synchronization:

1. **Platform → Composer** (Pull)
   - Fetch latest connector definitions
   - Retrieve requested status changes
   - Get configuration updates

2. **Composer → Platform** (Push)
   - Report actual container status
   - Send container logs
   - Update health metrics

## Update Mechanism

### Container Lifecycle Management

The update mechanism handles several scenarios:

#### 1. Deployment (Missing Container)
```rust
// Container doesn't exist but is defined in platform
async fn orchestrate_missing(connector) {
    orchestrator.deploy(connector).await;
    api.patch_status(connector.id, ConnectorStatus::Stopped).await;
}
```

#### 2. Status Alignment
```rust
// Align requested status with actual state
match (requested_status, current_status) {
    (RequestedStatus::Starting, ConnectorStatus::Stopped) => {
        orchestrator.start(container).await;
    }
    (RequestedStatus::Stopping, ConnectorStatus::Started) => {
        orchestrator.stop(container).await;
    }
}
```

#### 3. Configuration Updates
```rust
// Check if configuration hash changed
if requested_hash != current_hash {
    orchestrator.refresh(connector).await;  // Recreate with new config
}
```

#### 4. Health Monitoring
- Tracks container restart count
- Detects reboot loops (>3 restarts in <5 minutes)
- Reports health metrics every 30 seconds for running containers

#### 5. Log Collection
- Collects container logs every `logs_schedule` minutes
- Sends logs to platform for centralized monitoring
- Maintains log history for debugging

### Cleanup Process

The composer automatically removes orphaned containers:
```rust
// Remove containers not defined in platform
for container in orchestrator.list().await {
    if !platform_connectors.contains(container.id) {
        orchestrator.remove(container).await;
    }
}
```

## Configuration Encryption

### Hybrid Encryption Scheme

XTM Composer uses RSA/AES hybrid encryption for sensitive configuration:

```
Platform                    Composer
────────                    ────────
1. Generate AES key
2. Encrypt config with AES
3. Encrypt AES key with RSA public key
4. Send: [Version|Encrypted AES Key|Encrypted Data]
                            │
                            ▼
                    5. Decrypt AES key with RSA private key
                    6. Decrypt config with AES key
                    7. Use plaintext configuration
```

### Encryption Format

Encrypted values are Base64-encoded with structure:
```
[1 byte: Version][512 bytes: RSA-encrypted AES key+IV][N bytes: AES-encrypted data]
```

### Key Management

1. **RSA Private Key**
   - Loaded at startup from file or environment variable
   - PKCS#8 PEM format required
   - Used to decrypt AES keys

2. **AES Encryption**
   - AES-256-GCM for data encryption
   - Random key and IV per configuration value
   - Provides authenticated encryption


### Security Properties

- **Confidentiality**: Sensitive data encrypted at rest and in transit
- **Key Isolation**: Each configuration value has unique AES key
- **Forward Secrecy**: Compromised AES key doesn't affect other values
- **Authentication**: GCM mode provides data integrity

## Performance Considerations

1. **Async/Await**: All I/O operations are asynchronous
2. **Periodic Execution**: Configurable intervals prevent API flooding
3. **Batch Processing**: Multiple connectors processed in single cycle
4. **Resource Efficiency**: Rust's zero-cost abstractions minimize overhead
5. **Error Recovery**: Graceful handling of failures with retry logic

## Fault Tolerance

- **Health Checks**: Regular ping to platform ensures connectivity
- **Version Detection**: Re-registers on platform upgrade
- **Reboot Loop Detection**: Prevents infinite restart cycles
- **Graceful Shutdown**: Handles system signals properly
- **State Persistence**: Platform maintains desired state