# Release Readiness Checklist

Complete this checklist before releasing a Nebari software pack at v1.0. Copy this into a GitHub issue to track progress for your pack.

## Documentation

- [ ] README covers what the pack does, who it's for, and how to deploy it
- [ ] README includes prerequisites (Nebari version, namespace labeling, etc.)
- [ ] NebariApp values are documented (all configurable fields explained)
- [ ] Upstream chart values that users need to customize are documented
- [ ] Authentication setup is documented (if applicable)
- [ ] Troubleshooting section covers common failure modes
- [ ] CHANGELOG exists with notable pre-1.0 changes summarized

## Testing

- [ ] `helm lint` passes
- [ ] `helm template` renders correctly with NebariApp enabled and disabled
- [ ] Schema validation passes (kubeconform or equivalent)
- [ ] Standalone deployment test passes (no nebari-operator, `nebariapp.enabled=false`)
- [ ] Integration test passes with full Nebari stack (nebari-operator, Envoy Gateway, cert-manager, Keycloak)
- [ ] NebariApp reaches Ready condition with all sub-conditions healthy (RoutingReady, TLSReady, AuthReady)
- [ ] Health/readiness probes are configured and working
- [ ] Auth-protected routes reject unauthenticated requests (if auth enabled)
- [ ] Auth-protected routes allow authenticated users with correct group membership (if auth enabled)

## Security

- [ ] Containers do not run as root (or have documented justification if they must)
- [ ] No secrets are hardcoded in templates or default values
- [ ] OIDC scopes are minimally scoped to what the app needs
- [ ] Upstream container images are pinned to a specific tag or digest (not `latest`)
- [ ] NetworkPolicy or equivalent restricts unnecessary pod-to-pod communication (if applicable)
- [ ] SecurityContext sets `readOnlyRootFilesystem`, `runAsNonRoot`, `allowPrivilegeEscalation: false` where possible

## Examples and Values

- [ ] Example values file for Nebari deployment (`nebari-values.yaml`)
- [ ] Example values file for standalone deployment (`standalone-values.yaml`)
- [ ] ArgoCD Application example that references the published Helm repo
- [ ] All examples deploy successfully without modification (other than hostname)

## Publishing and Release Infrastructure

- [ ] Chart is publishable to `nebari-dev.github.io/helm-repository`
- [ ] Release workflow is configured and has been tested (at least one pre-1.0 release published)
- [ ] Chart version follows semver and is set to `1.0.0`
- [ ] `appVersion` in Chart.yaml matches the upstream application version being wrapped
- [ ] Helm repo index updates correctly after release
- [ ] Container images (if any custom ones) are published and accessible

## Manual Verification

- [ ] Deploys cleanly from the release commit to a cluster running the latest NIC release
- [ ] Application is accessible via the configured hostname with valid TLS
- [ ] Authentication flow works end-to-end (login, token propagation, logout) if auth enabled
- [ ] Application core functionality works as expected after deployment
- [ ] Upgrade from a fresh `helm install` to a second `helm upgrade` succeeds without errors

## Signoff

- [ ] Documented acceptance criteria exist (GitHub issue, spec, or equivalent)
- [ ] Product owner has verified all acceptance criteria are met
- [ ] Product owner has approved the release
