# Examples: Using the Simple App Helm Chart

This document provides practical examples for using the simple-app Helm chart.

## Basic Examples

### 1. Install with Default Values

```bash
helm install my-app ./simple-app
```

This installs the chart with:
- 2 replicas
- NGINX 1.25.3
- ClusterIP service
- Resource limits configured

### 2. Install for Development

```bash
helm install my-app ./simple-app -f simple-app/values-dev.yaml
```

Development configuration includes:
- 1 replica (for local testing)
- Debug logging enabled
- ConfigMap with dev settings
- Lower resource limits

### 3. Install for Production

```bash
helm install my-app ./simple-app -f simple-app/values-prod.yaml
```

Production configuration includes:
- 3 replicas minimum
- HPA enabled (scales 3-10 replicas)
- Pod anti-affinity for high availability
- Enhanced security settings
- Higher resource limits

## Advanced Examples

### 4. Override Multiple Values

```bash
helm install my-app ./simple-app \
  --set replicaCount=5 \
  --set image.tag=1.26.0 \
  --set resources.limits.memory=256Mi
```

### 5. Enable Ingress

```bash
helm install my-app ./simple-app \
  --set ingress.enabled=true \
  --set ingress.hosts[0].host=myapp.example.com \
  --set ingress.hosts[0].paths[0].path=/ \
  --set ingress.hosts[0].paths[0].pathType=Prefix
```

### 6. Use ConfigMap for Application Config

```bash
helm install my-app ./simple-app \
  --set configMap.enabled=true \
  --set configMap.data.DATABASE_URL=postgres://db:5432/myapp \
  --set configMap.data.CACHE_TTL=3600
```

### 7. Add Environment Variables

```bash
helm install my-app ./simple-app \
  --set env[0].name=API_KEY \
  --set env[0].value=secret-key-here \
  --set env[1].name=LOG_LEVEL \
  --set env[1].value=info
```

### 8. Enable Autoscaling

```bash
helm install my-app ./simple-app \
  --set autoscaling.enabled=true \
  --set autoscaling.minReplicas=2 \
  --set autoscaling.maxReplicas=8 \
  --set autoscaling.targetCPUUtilizationPercentage=75
```

## Custom Values File Example

Create a file `my-values.yaml`:

```yaml
replicaCount: 3

image:
  repository: nginx
  tag: "1.25.3"

service:
  type: LoadBalancer
  port: 80

ingress:
  enabled: true
  className: "nginx"
  hosts:
    - host: myapp.example.com
      paths:
        - path: /
          pathType: Prefix

resources:
  limits:
    cpu: 200m
    memory: 256Mi
  requests:
    cpu: 100m
    memory: 128Mi

configMap:
  enabled: true
  data:
    APP_NAME: "My Custom App"
    ENVIRONMENT: "production"
    DEBUG: "false"

env:
  - name: DATABASE_HOST
    value: postgres.default.svc.cluster.local
  - name: REDIS_HOST
    value: redis.default.svc.cluster.local
```

Then install with:

```bash
helm install my-app ./simple-app -f my-values.yaml
```

## Testing Examples

### Dry-run Installation

```bash
# See what would be installed without actually installing
helm install my-app ./simple-app --dry-run --debug
```

### Template Generation

```bash
# Generate Kubernetes manifests
helm template my-app ./simple-app > manifests.yaml

# Generate with specific values
helm template my-app ./simple-app -f simple-app/values-prod.yaml > prod-manifests.yaml
```

### Validate Against Kubernetes

```bash
# Generate and validate manifests
helm template my-app ./simple-app | kubectl apply --dry-run=client -f -
```

## Upgrade Examples

### Simple Upgrade

```bash
# Upgrade to new image version
helm upgrade my-app ./simple-app --set image.tag=1.26.0
```

### Upgrade with New Values File

```bash
# Switch from dev to staging
helm upgrade my-app ./simple-app -f simple-app/values-staging.yaml
```

### Upgrade and Reuse Values

```bash
# Keep existing values and only change replicas
helm upgrade my-app ./simple-app --reuse-values --set replicaCount=5
```

## Rollback Examples

```bash
# Rollback to previous version
helm rollback my-app

# Rollback to specific revision
helm rollback my-app 2

# Rollback with cleanup
helm rollback my-app --cleanup-on-fail
```

## Uninstall Examples

```bash
# Uninstall release
helm uninstall my-app

# Uninstall from specific namespace
helm uninstall my-app --namespace my-namespace

# Keep history for potential rollback
helm uninstall my-app --keep-history
```

## Namespace Examples

```bash
# Install in specific namespace (creates if doesn't exist)
helm install my-app ./simple-app --namespace my-app --create-namespace

# List releases in all namespaces
helm list --all-namespaces

# Get status in specific namespace
helm status my-app --namespace my-app
```

## Monitoring and Debugging

### Check Release Status

```bash
helm status my-app
```

### Get Release Values

```bash
# Get all values
helm get values my-app

# Get all values including defaults
helm get values my-app --all
```

### View Release History

```bash
helm history my-app
```

### View Generated Manifests

```bash
helm get manifest my-app
```

### Check for Updates

```bash
# See what would change
helm diff upgrade my-app ./simple-app -f new-values.yaml
```

Note: The `helm diff` command requires the helm-diff plugin:
```bash
helm plugin install https://github.com/databus23/helm-diff
```

## GitOps Integration

### ArgoCD Application Example

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: simple-app
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/linchen2chris/gitops-playground
    targetRevision: main
    path: simple-app
    helm:
      valueFiles:
        - values-prod.yaml
  destination:
    server: https://kubernetes.default.svc
    namespace: production
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

### Flux HelmRelease Example

```yaml
apiVersion: helm.toolkit.fluxcd.io/v2beta1
kind: HelmRelease
metadata:
  name: simple-app
  namespace: production
spec:
  interval: 5m
  chart:
    spec:
      chart: ./simple-app
      sourceRef:
        kind: GitRepository
        name: gitops-playground
      interval: 1m
  values:
    replicaCount: 3
    image:
      tag: "1.25.3"
  valuesFrom:
    - kind: ConfigMap
      name: simple-app-values
```

## Tips and Best Practices

1. **Always test with dry-run first**: `helm install my-app ./simple-app --dry-run`
2. **Use version control for values files**: Keep environment-specific values in Git
3. **Lint before installing**: `helm lint ./simple-app`
4. **Use meaningful release names**: Choose descriptive names for your releases
5. **Document custom values**: Add comments to your custom values files
6. **Test upgrades in lower environments first**: Test in dev/staging before production
7. **Monitor resource usage**: Adjust resource limits based on actual usage
8. **Use namespaces**: Isolate different environments with namespaces

## Troubleshooting

### Pods Not Starting

```bash
kubectl get pods -l app.kubernetes.io/instance=my-app
kubectl describe pod <pod-name>
kubectl logs <pod-name>
```

### Service Not Accessible

```bash
kubectl get svc
kubectl get endpoints
kubectl describe svc my-app
```

### Check Helm Release

```bash
helm status my-app
helm get all my-app
```

### View Generated Resources

```bash
kubectl get all -l app.kubernetes.io/instance=my-app
```
