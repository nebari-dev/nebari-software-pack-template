# Example 1: Basic Pack (Nginx)

The simplest possible Nebari Software Pack. Deploys a stock nginx container with
optional Nebari platform integration via the NebariApp CRD.

## What This Example Shows

- Minimum viable Helm chart structure for a Nebari pack
- The `nebariapp.yaml` template that creates a NebariApp custom resource
- How to toggle Nebari integration on/off with `nebariapp.enabled`
- Health probes, service, and deployment boilerplate

## Quick Start

### Deploy locally (no Nebari)

```bash
helm install test-basic ./chart/

# Access via port-forward
kubectl port-forward svc/test-basic-my-pack 8080:80
# Open http://localhost:8080
```

### Deploy on Nebari

```bash
helm install my-pack ./chart/ \
  --set nebariapp.enabled=true \
  --set nebariapp.hostname=my-pack.nebari.example.com
```

The nebari-operator will automatically create:
- An HTTPRoute directing traffic from `my-pack.nebari.example.com` to your service
- A TLS certificate via cert-manager

### Deploy on Nebari with authentication

```bash
helm install my-pack ./chart/ \
  --set nebariapp.enabled=true \
  --set nebariapp.hostname=my-pack.nebari.example.com \
  --set nebariapp.auth.enabled=true
```

This additionally creates:
- A Keycloak OIDC client (auto-provisioned)
- An Envoy Gateway SecurityPolicy that requires login before accessing the app

## Files

| File | Purpose |
|------|---------|
| `chart/Chart.yaml` | Helm chart metadata |
| `chart/values.yaml` | Default configuration values |
| `chart/templates/_helpers.tpl` | Name, label, and selector helpers |
| `chart/templates/nebariapp.yaml` | NebariApp CRD (conditional on `nebariapp.enabled`) |
| `chart/templates/deployment.yaml` | Kubernetes Deployment for nginx |
| `chart/templates/service.yaml` | ClusterIP Service |
| `chart/templates/NOTES.txt` | Post-install instructions |

## Customizing

To use this as a starting point for your own pack:

1. Replace `my-pack` with your pack name in all files
2. Replace the nginx image with your application image
3. Update the container port and health probe paths as needed
4. Set `nebariapp.hostname` to your desired domain

See the [main README](../../README.md) for the full customization guide.
