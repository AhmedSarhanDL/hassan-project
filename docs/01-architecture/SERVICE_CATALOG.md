# Service Catalog

This document provides a one-pager overview of each microservice in the Egypt Edututor platform.

## Service Overview

Each service is designed following microservices principles with clear boundaries and single responsibility.

---

## Core Services

### API Gateway
- **Purpose**: Request routing, authentication, rate limiting
- **Technology**: FastAPI
- **Port**: 8000

### Chat API
- **Purpose**: LLM interactions, conversation management
- **Technology**: FastAPI + LLM Router
- **Port**: 8001

### Speech API
- **Purpose**: Speech-to-text and text-to-speech processing
- **Technology**: FastAPI + Google Cloud Speech
- **Port**: 8002

### Billing Service
- **Purpose**: Subscription management, payment processing
- **Technology**: FastAPI + Paymob/Stripe
- **Port**: 8003

### Auth Service
- **Purpose**: User authentication and authorization
- **Technology**: FastAPI + Keycloak
- **Port**: 8004

### KG Ingest Service
- **Purpose**: Knowledge graph content ingestion
- **Technology**: Python + Neo4j + Weaviate
- **Port**: 8005

---

## Frontend Applications

### Web App
- **Purpose**: Main student/teacher interface
- **Technology**: Next.js + React
- **Port**: 3000

### Teacher Portal
- **Purpose**: Teacher dashboard and analytics
- **Technology**: Next.js + React
- **Port**: 3001

### Admin Portal
- **Purpose**: System administration interface
- **Technology**: Next.js + React
- **Port**: 3002 
