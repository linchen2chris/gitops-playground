# Simple App Helm Chart

A simple Helm chart example for the GitOps Playground, demonstrating Kubernetes deployment patterns using Helm.

## Overview

This Helm chart deploys a simple NGINX-based web application with configurable parameters for different environments. It's designed as a learning resource for GitOps practices and Helm chart development.

## Features

- **Deployment**: Configurable replica count with rolling update strategy
- **Service**: ClusterIP service with customizable ports
- **Ingress**: Optional ingress configuration for external access
- **ConfigMap & Secret**: Support for configuration and sensitive data
- **Security**: Pod security contexts and container security settings
- **Autoscaling**: Optional Horizontal Pod Autoscaler (HPA)
- **Probes**: Liveness and readiness probes for health checks
- **Environment-specific**: Pre-configured values for dev, staging, and production

## Prerequisites

- Kubernetes 1.20+
- Helm 3.0+
- kubectl configured to communicate with your cluster

## Installation

### Basic Installation

```bash
# Install with default values
helm install simple-app ./simple-app

# Install in a specific namespace
helm install simple-app ./simple-app --namespace my-namespace --create-namespace
```

### Environment-Specific Installation

```bash
# Development environment
helm install simple-app ./simple-app -f simple-app/values-dev.yaml

# Staging environment
helm install simple-app ./simple-app -f simple-app/values-staging.yaml

# Production environment
helm install simple-app ./simple-app -f simple-app/values-prod.yaml
```

### Custom Values

```bash
# Override specific values
helm install simple-app ./simple-app \
  --set replicaCount=3 \
  --set image.tag=1.25.3 \
  --set ingress.enabled=true

# Use a custom values file
helm install simple-app ./simple-app -f my-custom-values.yaml
```

## Configuration

### Key Parameters

| Parameter | Description | Default |
|-----------|-------------|---------|
| `replicaCount` | Number of replicas | `2` |
| `image.repository` | Container image repository | `nginx` |
| `image.tag` | Container image tag | `1.25.3` |
| `image.pullPolicy` | Image pull policy | `IfNotPresent` |
| `service.type` | Kubernetes service type | `ClusterIP` |
| `service.port` | Service port | `80` |
| `service.targetPort` | Container port | `8080` |
| `ingress.enabled` | Enable ingress | `false` |
| `ingress.className` | Ingress class name | `nginx` |
| `resources.limits.cpu` | CPU limit | `100m` |
| `resources.limits.memory` | Memory limit | `128Mi` |
| `autoscaling.enabled` | Enable HPA | `false` |
| `configMap.enabled` | Enable ConfigMap | `false` |
| `secret.enabled` | Enable Secret | `false` |

### Full Configuration

See [values.yaml](values.yaml) for all available configuration options.

## Usage Examples

### Accessing the Application

After installation, follow the instructions displayed in the NOTES to access your application.

For ClusterIP service (default):

```bash
export POD_NAME=$(kubectl get pods -l "app.kubernetes.io/name=simple-app" -o jsonpath="{.items[0].metadata.name}")
kubectl port-forward $POD_NAME 8080:8080
# Access at http://localhost:8080
```

### Upgrading the Release

```bash
# Upgrade with new values
helm upgrade simple-app ./simple-app -f simple-app/values-prod.yaml

# Upgrade and change image tag
helm upgrade simple-app ./simple-app --set image.tag=1.26.0
```

### Viewing Release Information

```bash
# List all releases
helm list

# Get release status
helm status simple-app

# Get release values
helm get values simple-app

# Get release history
helm history simple-app
```

### Rolling Back

```bash
# Rollback to previous release
helm rollback simple-app

# Rollback to specific revision
helm rollback simple-app 2
```

### Uninstalling

```bash
# Uninstall the release
helm uninstall simple-app

# Uninstall from specific namespace
helm uninstall simple-app --namespace my-namespace
```

## Development

### Testing the Chart

```bash
# Lint the chart
helm lint ./simple-app

# Template the chart (dry-run)
helm template simple-app ./simple-app

# Template with specific values
helm template simple-app ./simple-app -f simple-app/values-dev.yaml

# Install in debug mode
helm install simple-app ./simple-app --dry-run --debug
```

### Validating Templates

```bash
# Generate and validate Kubernetes manifests
helm template simple-app ./simple-app | kubectl apply --dry-run=client -f -
```

### Packaging

```bash
# Package the chart
helm package ./simple-app

# This creates simple-app-0.1.0.tgz
```

## Environment Configurations

### Development (values-dev.yaml)
- 1 replica
- Lower resource limits
- Debug logging enabled
- ConfigMap with debug settings

### Staging (values-staging.yaml)
- 2 replicas
- Moderate resource limits
- Info level logging
- ConfigMap with staging settings

### Production (values-prod.yaml)
- 3 replicas (with autoscaling)
- Higher resource limits
- Warning level logging
- Pod anti-affinity rules
- TLS/SSL support
- Enhanced security settings

## Architecture

```
simple-app/
├── Chart.yaml              # Chart metadata
├── values.yaml             # Default configuration values
├── values-dev.yaml         # Development environment values
├── values-staging.yaml     # Staging environment values
├── values-prod.yaml        # Production environment values
├── README.md               # This file
└── templates/
    ├── _helpers.tpl        # Template helpers
    ├── deployment.yaml     # Deployment manifest
    ├── service.yaml        # Service manifest
    ├── ingress.yaml        # Ingress manifest
    ├── configmap.yaml      # ConfigMap manifest
    ├── secret.yaml         # Secret manifest
    ├── serviceaccount.yaml # ServiceAccount manifest
    ├── hpa.yaml            # HorizontalPodAutoscaler manifest
    ├── NOTES.txt           # Post-installation notes
    └── tests/
        └── test-connection.yaml  # Helm test
```

## Troubleshooting

### Common Issues

1. **Pods not starting**: Check pod logs and events
```bash
kubectl get pods
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

2. **Service not accessible**: Verify service and endpoints
```bash
kubectl get svc simple-app
kubectl get endpoints simple-app
```

3. **Helm installation fails**: Use debug mode
```bash
helm install simple-app ./simple-app --debug
```

## Contributing

This is an example chart for learning purposes. Feel free to fork and modify for your needs.

## License

See the [LICENSE](../LICENSE) file in the repository root.

## Resources

- [Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [GitOps Principles](https://www.gitops.tech/)

## Support

For issues and questions, please open an issue in the [GitHub repository](https://github.com/linchen2chris/gitops-playground).
