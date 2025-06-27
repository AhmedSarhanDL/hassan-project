# Terraform Guide

This document provides a guide for using Terraform to manage cloud infrastructure for the Egypt Edututor platform.

## Overview

Terraform is used for:
- Provisioning AWS resources (EKS, RDS, S3, VPC, etc.)
- Managing infrastructure as code (IaC)
- Versioning and reproducibility of environments
- Automated deployment and rollback

## Best Practices
- Use remote state storage (S3) with locking (DynamoDB)
- Organize modules by service (eks, rds, s3, vpc, etc.)
- Use variables and workspaces for staging/production
- Tag resources for cost allocation
- Use `terraform fmt` and `terraform validate` in CI

## Example Directory Structure

```
infra/
  terraform/
    main.tf
    variables.tf
    outputs.tf
    modules/
      eks/
      rds/
      s3/
      vpc/
```

## Common Commands

```bash
# Initialize Terraform
terraform init

# Plan changes
terraform plan -var-file=staging.tfvars

# Apply changes
terraform apply -var-file=staging.tfvars

# Destroy resources
terraform destroy -var-file=staging.tfvars
```

## Acceptance Criteria
- [ ] All infrastructure is managed via Terraform
- [ ] State is stored remotely and locked
- [ ] CI/CD pipeline runs `terraform fmt` and `terraform validate`
- [ ] Rollback is possible for failed changes

## See Also
- [K8s Overview](K8S_OVERVIEW.md)
- [CI/CD Pipeline](CI_CD.md)
- [Cost Monitoring](COST_MONITORING.md)

