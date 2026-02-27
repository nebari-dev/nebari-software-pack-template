# Local development Makefile for Nebari Software Pack examples
#
# Prerequisites: docker, k3d, ctlptl, tilt, helm
#
# Usage:
#   make up-basic      Deploy the basic nginx example
#   make up-fastapi    Build + deploy the FastAPI example
#   make up-podinfo    Deploy the podinfo wrapper example
#   make down          Tear down the cluster and stop Tilt

CHART_BASIC   := ../examples/basic-nginx/chart
CHART_FASTAPI := ../examples/auth-fastapi/chart
CHART_PODINFO := ../examples/wrap-existing-chart/chart
CLUSTER_NAME  := k3d-my-pack-dev

.PHONY: up-basic up-fastapi up-podinfo down cluster

# Create the k3d cluster (idempotent)
cluster:
	ctlptl apply -f ctlptl-config.yaml

# Deploy basic nginx example
up-basic: cluster
	helm upgrade --install my-pack $(CHART_BASIC) \
		--set nebariapp.enabled=false \
		--wait --timeout 2m
	@echo ""
	@echo "Basic nginx deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack-my-pack 8080:80"
	@echo "  open http://localhost:8080"

# Build and deploy auth-aware FastAPI example
up-fastapi: cluster
	docker build -t localhost:5005/my-pack-fastapi:dev ../examples/auth-fastapi/
	docker push localhost:5005/my-pack-fastapi:dev
	helm upgrade --install my-pack $(CHART_FASTAPI) \
		--set nebariapp.enabled=false \
		--set image.repository=localhost:5005/my-pack-fastapi \
		--set image.tag=dev \
		--wait --timeout 2m
	@echo ""
	@echo "FastAPI app deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack-my-pack 8080:80"
	@echo "  open http://localhost:8080"

# Deploy podinfo wrapper example
up-podinfo: cluster
	helm dependency update $(CHART_PODINFO)
	helm upgrade --install my-pack $(CHART_PODINFO) \
		--set nebariapp.enabled=false \
		--wait --timeout 2m
	@echo ""
	@echo "Podinfo deployed. Access via:"
	@echo "  kubectl port-forward svc/my-pack-podinfo 9898:9898"
	@echo "  open http://localhost:9898"

# Tear down everything
down:
	-helm uninstall my-pack 2>/dev/null || true
	-tilt down 2>/dev/null || true
	-pkill -f "tilt up" 2>/dev/null || true
	ctlptl delete -f ctlptl-config.yaml
