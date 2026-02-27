# Local development Makefile for Nebari Software Pack examples
#
# Prerequisites: docker, kind, helm, kubectl
#
# Usage:
#   make up-vanilla    Deploy the vanilla YAML example
#   make up-kustomize  Deploy the kustomize example (dev overlay)
#   make up-basic      Deploy the basic nginx Helm example
#   make up-fastapi    Deploy the FastAPI Helm example (pre-built GHCR image)
#   make up-podinfo    Deploy the podinfo wrapper Helm example
#   make down          Delete the kind cluster

VANILLA_DIR   := ../examples/vanilla-yaml
KUSTOMIZE_DIR := ../examples/kustomize-nginx/overlays/dev
CHART_BASIC   := ../examples/basic-nginx/chart
CHART_FASTAPI := ../examples/auth-fastapi/chart
CHART_PODINFO := ../examples/wrap-existing-chart/chart
CLUSTER_NAME  := my-pack-dev

.PHONY: up-vanilla up-kustomize up-basic up-fastapi up-podinfo down cluster

# Create the kind cluster (idempotent)
cluster:
	kind get clusters | grep -q "^$(CLUSTER_NAME)$$" || kind create cluster --name $(CLUSTER_NAME)

# Deploy vanilla YAML example (Deployment + Service only, no NebariApp)
up-vanilla: cluster
	kubectl apply -f $(VANILLA_DIR)/deployment.yaml \
		-f $(VANILLA_DIR)/service.yaml
	kubectl wait --for=condition=ready pod -l app=my-pack --timeout=120s
	@echo ""
	@echo "Vanilla nginx deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack 8080:80"
	@echo "  open http://localhost:8080"

# Deploy kustomize example (dev overlay, without NebariApp)
up-kustomize: cluster
	kubectl apply -f $(KUSTOMIZE_DIR)/../../base/deployment.yaml \
		-f $(KUSTOMIZE_DIR)/../../base/service.yaml
	kubectl wait --for=condition=ready pod -l app=my-pack --timeout=120s
	@echo ""
	@echo "Kustomize nginx deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack 8080:80"
	@echo "  open http://localhost:8080"

# Deploy basic nginx Helm example
up-basic: cluster
	helm upgrade --install my-pack $(CHART_BASIC) \
		--set nebariapp.enabled=false \
		--wait --timeout 2m
	@echo ""
	@echo "Basic nginx deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack-my-pack 8080:80"
	@echo "  open http://localhost:8080"

# Deploy auth-aware FastAPI Helm example (uses pre-built GHCR image)
up-fastapi: cluster
	helm upgrade --install my-pack $(CHART_FASTAPI) \
		--set nebariapp.enabled=false \
		--wait --timeout 2m
	@echo ""
	@echo "FastAPI app deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack-my-pack 8080:80"
	@echo "  open http://localhost:8080"

# Deploy podinfo wrapper Helm example
up-podinfo: cluster
	helm dependency update $(CHART_PODINFO)
	helm upgrade --install my-pack $(CHART_PODINFO) \
		--set nebariapp.enabled=false \
		--wait --timeout 2m
	@echo ""
	@echo "Podinfo deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack-podinfo 9898:9898"
	@echo "  open http://localhost:9898"

# Delete the kind cluster
down:
	kind delete cluster --name $(CLUSTER_NAME)
