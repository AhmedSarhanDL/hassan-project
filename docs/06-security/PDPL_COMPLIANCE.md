# PDPL Compliance

This document outlines compliance with the Egyptian Personal Data Protection Law (PDPL) for the Egypt Edututor platform.

## Overview

The platform is designed to:
- Protect student and guardian personal data
- Obtain explicit consent for data processing
- Enable data access, correction, and deletion
- Store data within Egypt (where required)
- Respond to data subject requests within 15 days

## Key Requirements

### 1. Lawful Basis for Processing
- Obtain explicit consent from guardians for minors
- Document consent and processing purposes
- Allow withdrawal of consent at any time

### 2. Data Minimization
- Collect only necessary data (name, grade, contact)
- Limit retention to educational use period
- Anonymize or delete data after retention period

### 3. Data Subject Rights
- Right to access, correct, and delete data
- Right to object to processing
- Right to data portability

### 4. Security Measures
- Encrypt data at rest and in transit
- Use strong authentication for access
- Log and monitor data access

### 5. Data Breach Response
- Notify NTRA and affected users within 72 hours
- Document breach details and mitigation steps

## Implementation Tasks
- [ ] Guardian consent workflow in onboarding
- [ ] Data access and deletion APIs
- [ ] Data residency controls for Egypt
- [ ] Breach notification playbook
- [ ] Staff training on PDPL compliance

## See Also
- [Privacy Impact Assessment](PRIVACY_IMPACT_ASSESSMENT.md)
- [Secrets Management](SECRETS_MANAGEMENT.md)

