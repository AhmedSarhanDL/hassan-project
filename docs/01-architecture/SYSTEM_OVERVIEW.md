# System Overview

## Architecture Summary
The platform uses a microservices architecture with:
- API Gateway (FastAPI)
- Microservices for chat, speech, billing, auth, etc.
- Knowledge Graph (Neo4j), Vector DB (Weaviate), Relational DB (Postgres)
- LLM router for model selection (OpenAI, Mixtral)
- Frontend (Next.js PWA)
- Kubernetes for orchestration

## Data Flow
1. User query (text/speech) → API Gateway
2. Gateway authenticates, logs usage, routes to chat_api
3. chat_api: vector search (Weaviate) + KG hop (Neo4j), context composition
4. LLM router chooses model, returns answer
5. Usage metered, response streamed to client
6. Teacher can view KG path for answer
7. Metrics exported to Prometheus/Grafana
8. Billing aggregates usage for invoicing

## Main Components
- API Gateway
- chat_api
- speech_api
- billing
- auth
- kg_ingest
- Frontend (web, teacher-portal, admin)
- Monitoring/Observability stack

## See also
- [Service Catalog](SERVICE_CATALOG.md)
- [C4 Diagrams](ADR/) 
