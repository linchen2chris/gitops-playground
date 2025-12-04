# gitops-playground

A GitOps playground repository for learning and experimenting with GitOps practices, Helm charts, and Kubernetes deployments.

## Contents

### Simple App Helm Chart

A comprehensive example Helm chart demonstrating:
- Kubernetes deployment patterns
- Environment-specific configurations (dev, staging, production)
- ConfigMaps and Secrets management
- Ingress and service configuration
- Horizontal Pod Autoscaling
- Security best practices

See [simple-app/README.md](simple-app/README.md) for detailed documentation.

## Quick Start

### Prerequisites

- Kubernetes cluster (local or remote)
- Helm 3.0+
- kubectl configured

### Install the Simple App

```bash
# Install with default values
helm install simple-app ./simple-app

# Install for development environment
helm install simple-app ./simple-app -f simple-app/values-dev.yaml

# Install for production environment
helm install simple-app ./simple-app -f simple-app/values-prod.yaml
```

### Validate the Chart

```bash
# Lint the chart
helm lint ./simple-app

# Generate templates without installing
helm template simple-app ./simple-app
```

## Resources

- [Helm Documentation](https://helm.sh/docs/)
- [Kubernetes Documentation](https://kubernetes.io/docs/)
- [GitOps Principles](https://www.gitops.tech/)

## License

See [LICENSE](LICENSE) file for details.