# Example 3: Wrapping an Existing Chart (Podinfo)

This is the most realistic use case for a Nebari Software Pack. Instead of writing
your own Deployment and Service templates, you add an existing Helm chart as a
dependency and create a NebariApp resource that points to its service.

**Key lesson: You don't rewrite the app - you just connect it to Nebari.**

## What This Example Shows

- Adding an upstream Helm chart as a `Chart.yaml` dependency
- Overriding upstream values via your own `values.yaml`
- Creating a NebariApp that points to the upstream chart's service
- No custom Deployment or Service templates needed

## How It Works

```
Chart.yaml
  dependencies:
    - name: podinfo          # Upstream chart handles pods + services
      version: 6.10.1
      repository: oci://ghcr.io/stefanprodan/charts

templates/
  nebariapp.yaml             # Points to podinfo's service -> Nebari routes to it
  (no deployment.yaml!)      # Podinfo chart handles this
  (no service.yaml!)         # Podinfo chart handles this
```

The only template you write is `nebariapp.yaml`. Everything else comes from the
upstream chart.

## Quick Start

### Build dependencies

```bash
helm dependency update ./chart/
```

### Deploy locally (no Nebari)

```bash
helm install test-podinfo ./chart/

# Access via port-forward
kubectl port-forward svc/test-podinfo-podinfo 9898:9898
# Open http://localhost:9898
```

### Deploy on Nebari

```bash
helm install my-pack ./chart/ \
  --set nebariapp.enabled=true \
  --set nebariapp.hostname=my-pack.nebari.example.com
```

### Override upstream values

```bash
helm install my-pack ./chart/ \
  --set nebariapp.enabled=true \
  --set nebariapp.hostname=my-pack.nebari.example.com \
  --set podinfo.ui.message="Custom greeting!"
```

## Files

| File | Purpose |
|------|---------|
| `chart/Chart.yaml` | Helm chart metadata with podinfo dependency |
| `chart/values.yaml` | NebariApp config + podinfo overrides |
| `chart/templates/_helpers.tpl` | Name, label, and selector helpers |
| `chart/templates/nebariapp.yaml` | NebariApp CRD pointing to podinfo's service |
| `chart/templates/NOTES.txt` | Post-install instructions |

## Adapting for Your Own Chart

To wrap a different upstream chart:

1. Replace the `dependencies` entry in `Chart.yaml` with your chart
2. Update `nebariapp.service.port` to match the upstream service port
3. Update the `my-pack.podinfo-service-name` helper in `_helpers.tpl` to match
   the upstream chart's service naming convention
4. Add any value overrides under the dependency's key in `values.yaml`
5. Run `helm dependency update ./chart/`

See the [main README](../../README.md) for the full customization guide.
