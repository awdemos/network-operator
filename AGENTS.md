# AMD Network Operator — Agent Guide

## Overview

Kubernetes operator for deploying and managing AMD AINIC networking components
(RDMA, SR-IOV, device plugins, secondary networks). Built with Go, kubebuilder,
controller-runtime, and Kustomize.

## Project Layout

| Path | Purpose |
|------|---------|
| `api/` | CRD Go types (`NetworkConfig`) |
| `cmd/` | Manager entrypoint |
| `internal/` | Controllers, KMM module helpers, reconciler logic |
| `config/` | Kustomize bases/overlays, CRDs, RBAC, samples |
| `helm-charts-k8s/` | Helm chart for Kubernetes deployments |
| `docker/` | Container image build support |
| `docs/` | Sphinx documentation |
| `Makefile` | Primary build/test/deploy interface |

## Build Commands

```bash
# Build the manager binary
make manager

# Or directly with Go
go build ./cmd/... ./api/... ./internal/...
```

## Test Commands

```bash
# Run unit tests (Ginkgo/Gomega suites)
make unit-test

# Vet only
make vet

# Go vet on main packages (e2e may have compile errors in this fork)
go vet ./cmd/... ./api/... ./internal/...
```

## Lint / Generate

```bash
# Format
make fmt

# Generate deepcopy + mocks
make generate

# Generate CRDs + RBAC
make manifests

# golangci-lint (downloads binary if needed)
make lint
```

## Key Conventions

- **CRD versioning**: `api/v1alpha1/networkconfig_types.go` defines the API.
- **KMM integration**: `internal/kmmmodule/` builds KernelModule objects for driver management.
- **Vendor directory**: dependencies are vendored (`vendor/`); `GOFLAGS="-mod=mod"` is set in Makefile.
- **Submodule**: `external/common-infra-operator` is a git submodule used via `replace` in `go.mod`.

## Common Gotchas

- `go build ./...` currently fails in `tests/e2e/` due to missing helper methods in this snapshot.
  Build/test the main packages with the commands above instead.
- `go.mod` declares Go 1.25.8; the CNI plugins submodule intentionally stays on Go 1.23.4.
- `make all` runs `vendor generate manager manifests helm-k8s docker-build` — slow first run.

## Deployment

Use the Makefile targets:

```bash
# Build container image
make docker-build

# Install CRDs to current kube context
make install

# Deploy controller
make deploy
```

No GitHub Actions/Dagger workflow is present in this fork; use local `make` and
container tooling.
