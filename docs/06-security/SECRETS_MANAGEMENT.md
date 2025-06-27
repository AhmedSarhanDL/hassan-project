# Secrets Management

This document describes the secrets management strategy for the Egypt Edututor platform.

## Overview

Secrets management ensures:
- Secure storage of API keys, credentials, and tokens
- Least-privilege access for services and users
- Audit logging of secret access
- Automated rotation of secrets

## Tools & Practices
- Use AWS Secrets Manager for cloud secrets
- Store local dev secrets in `.env` (never committed)
- Encrypt secrets at rest and in transit
- Use IAM roles for service-to-service auth
- Rotate secrets every 90 days

## Access Controls
- RBAC for secret access
- Approval workflow for production secrets
- Monitor and alert on secret access

## Acceptance Criteria
- [ ] All secrets stored securely (no hardcoded secrets)
- [ ] Access is logged and monitored
- [ ] Secrets rotated regularly

## See Also
- [PDPL Compliance](PDPL_COMPLIANCE.md)
- [Pen Test Playbook](PEN_TEST_PLAYBOOK.md)

