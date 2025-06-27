# CI/CD Pipeline

This document describes the Continuous Integration and Continuous Deployment (CI/CD) strategy for the Egypt Edututor platform.

## Overview

The CI/CD pipeline ensures:
- Automated testing and linting on every commit
- Secure build and deployment to staging/production
- Infrastructure as Code (IaC) for reproducible environments
- Rollback and canary deployment support

## Pipeline Stages

### 1. Code Quality & Testing
- Run pre-commit hooks (black, isort, flake8)
- Run unit and integration tests (pytest, coverage)
- Lint Dockerfiles and YAML configs

### 2. Build & Artifact Management
- Build Docker images for backend and frontend
- Tag images with commit SHA and version
- Push images to container registry (GitHub Packages, AWS ECR)

### 3. Infrastructure Provisioning
- Use Terraform for cloud resources (EKS, RDS, S3, etc.)
- Apply changes in staging, then production
- Store Terraform state in S3 with locking

### 4. Deployment
- Deploy to Kubernetes (ArgoCD or GitHub Actions)
- Run database migrations (Alembic, custom scripts)
- Health checks and smoke tests post-deploy

### 5. Monitoring & Rollback
- Monitor with Prometheus, Grafana, Sentry
- Rollback on failed health checks
- Canary deploys for new features

## GitHub Actions Example

```yaml
name: CI/CD Pipeline
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.11'
      - name: Install dependencies
        run: |
          pip install -r src/poc/backend/requirements.txt
      - name: Lint
        run: |
          black --check src/poc/backend
          flake8 src/poc/backend
      - name: Test
        run: |
          pytest --cov=src/poc/backend
      - name: Build Docker image
        run: |
          docker build -t edututor-backend:${{ github.sha }} src/poc/backend
      - name: Push Docker image
        run: |
          echo ${{ secrets.GITHUB_TOKEN }} | docker login ghcr.io -u ${{ github.actor }} --password-stdin
          docker tag edututor-backend:${{ github.sha }} ghcr.io/ahmedsarhandl/edututor-backend:${{ github.sha }}
          docker push ghcr.io/ahmedsarhandl/edututor-backend:${{ github.sha }}
```

## Acceptance Criteria
- [ ] All code changes are tested and linted automatically
- [ ] Docker images are built and pushed on every commit
- [ ] Infrastructure is provisioned via Terraform
- [ ] Rollback is possible on failed deploys
- [ ] Monitoring and alerting are enabled

## See Also
- [Terraform Guide](TERRAFORM_GUIDE.md)
- [K8s Overview](K8S_OVERVIEW.md)
- [Cost Monitoring](COST_MONITORING.md)

