# Installation Guide

## System Requirements

### Runtime Requirements

- **Container Runtime**: One of the following:
  - Docker v20.10 or higher
  - Kubernetes v1.24 or higher
  - Portainer v2.0 or higher (for Docker management)

### Security Requirements

- **RSA Private Key**: 4096-bit RSA private key for authentication
- **Network Access**: 
  - Connectivity to OpenCTI/OpenBAS instances
  - Access to container orchestration API
- **Permissions**: 
  - Container management permissions
  - Socket access for Docker mode

## Installation Methods

Create a configuration file based on your environment or add extra environment variables in the following steps.
See [Configuration Reference](configuration.md) for more information on required configuration. 

### Docker Installation

```bash
# Pull the latest image
docker pull filigran/xtm-composer:latest

# Create configuration directory
mkdir -p /opt/xtm-composer/config

# Generate RSA private key
openssl genrsa -out /opt/xtm-composer/private_key_4096.pem 4096

# Run container
docker run -d \
  --name xtm-composer \
  -v /opt/xtm-composer/config:/config \
  -v /opt/xtm-composer/private_key_4096.pem:/keys/private_key.pem \
  -e COMPOSER_ENV=production \
  filigran/xtm-composer:latest
```

### Kubernetes Installation

Note: The Kubernetes installation method described here assumes that OpenCTI is already deployed on a Kubernetes cluster.

1. Create namespace:
```bash
kubectl create namespace xtm-composer
```

2. Create secret for RSA key:
```bash
# Generate key
openssl genrsa -out private_key_4096.pem 4096

# Create secret
kubectl create secret generic xtm-composer-keys \
  --from-file=private_key.pem=private_key_4096.pem \
  -n xtm-composer
```

3. Create ConfigMap for configuration:
```bash
kubectl create configmap xtm-composer-config \
  --from-file=default.yaml=config/default.yaml \
  -n xtm-composer
```

4. Create service account:

XTM Composer uses a service account to have authorization to start new pods and deployments on the cluster. 

```bash
cat <<EOF | kubectl apply -n xtm-composer -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: xtm-composer
---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: xtm-composer
rules:
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "watch", "list", "create", "update", "patch", "delete", "deletecollection"]
- apiGroups: [""]
  resources: ["pods", "pods/log"]
  verbs: ["get", "watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: xtm-composer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: xtm-composer
subjects:
- kind: ServiceAccount
  name: xtm-composer
EOF
```

5. Deploy XTM Composer:
```bash
cat <<EOF | kubectl apply -n xtm-composer -f -
apiVersion: apps/v1
kind: Deployment
metadata:
  name: xtm-composer
  namespace: xtm-composer
spec:
  replicas: 1
  selector:
    matchLabels:
      app: xtm-composer
  template:
    metadata:
      labels:
        app: xtm-composer
    spec:
      containers:
      - name: xtm-composer
        image: filigran/xtm-composer:latest
        env:
        - name: COMPOSER_ENV
          value: "production"
        - name: MANAGER__ID
          value: "k8s-manager"
        - name: MANAGER__CREDENTIALS_KEY_FILEPATH
          value: "/keys/private_key.pem"
        volumeMounts:
        - name: config
          mountPath: /config
        - name: keys
          mountPath: /keys
      volumes:
      - name: config
        configMap:
          name: xtm-composer-config
      - name: keys
        secret:
          secretName: xtm-composer-keys
      serviceAccountName: xtm-composer
EOF
```

### Binary Installation

#### Prerequisites

Install Rust (1.70.0 or higher):
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source $HOME/.cargo/env
```

#### Build from Source

```bash
# Clone repository
git clone https://github.com/OpenCTI-Platform/xtm-composer.git
cd xtm-composer

# Build release binary
cargo build --release

# Generate RSA key
openssl genrsa -out ./private_key_4096.pem 4096
```

## Post-Installation

### Verify Installation

Check logs to ensure XTM Composer is running:

**Docker:**
```bash
docker logs xtm-composer
```

**Kubernetes:**
```bash
kubectl logs -n xtm-composer deployment/xtm-composer
```


### Next Steps

1. Configure XTM Composer - See [Configuration Reference](configuration.md)
2. Connect to OpenCTI/OpenBAS - See [Quick Start](quick-start.md)
3. Verify integration management functionality
