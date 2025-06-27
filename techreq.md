# Technical Requirements Document
# Egypt-Focused LLM Tutoring Platform

## Document Information
- **Version**: 1.0
- **Date**: 2024
- **Owner**: Development Team
- **Status**: Draft

---

## 1. System Architecture Requirements

### 1.1 Overall Architecture
**Requirement**: Microservices architecture with clear separation of concerns
**Priority**: Critical

#### Technical Specifications:
- **API Gateway**: FastAPI with rate limiting and authentication
- **Service Communication**: REST APIs + gRPC for internal services
- **Message Queue**: Redis for async tasks
- **Load Balancer**: NGINX with SSL termination
- **Container Orchestration**: Kubernetes (K8s)

#### Acceptance Criteria:
- [ ] All services are containerized and deployable independently
- [ ] API Gateway routes requests to appropriate services
- [ ] Service discovery works automatically via K8s
- [ ] Health checks return 200 for all services
- [ ] Circuit breaker pattern implemented for external dependencies
- [ ] Request tracing works end-to-end

#### Tasks:
1. Set up monorepo structure with service separation
2. Configure Docker multi-stage builds for each service
3. Implement API Gateway with FastAPI
4. Set up Kubernetes manifests and Helm charts
5. Configure NGINX ingress controller
6. Implement health check endpoints for all services

---

## 2. Database Requirements

### 2.1 PostgreSQL Schema
**Requirement**: Relational database for user data, billing, and analytics
**Priority**: Critical

#### Schema Design:
```sql
-- Users and Authentication
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT NOW(),
    last_login TIMESTAMP,
    status VARCHAR(20) DEFAULT 'active',
    profile JSONB
);

CREATE TABLE guardian_consents (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    guardian_email VARCHAR(255) NOT NULL,
    consent_type VARCHAR(50) NOT NULL,
    consent_given BOOLEAN NOT NULL,
    consent_timestamp TIMESTAMP DEFAULT NOW(),
    legal_text_hash VARCHAR(64) NOT NULL,
    ip_address INET
);

-- Learning Analytics
CREATE TABLE learning_profiles (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    subject VARCHAR(100) NOT NULL,
    concept VARCHAR(200) NOT NULL,
    mastery_score FLOAT DEFAULT 0.0,
    last_interaction TIMESTAMP DEFAULT NOW(),
    total_interactions INTEGER DEFAULT 0,
    streak_days INTEGER DEFAULT 0,
    UNIQUE(user_id, subject, concept)
);

-- Usage Tracking
CREATE TABLE usage_logs (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    session_id UUID NOT NULL,
    service_name VARCHAR(50) NOT NULL,
    tokens_used INTEGER DEFAULT 0,
    speech_seconds INTEGER DEFAULT 0,
    timestamp TIMESTAMP DEFAULT NOW(),
    cost_usd DECIMAL(10,6) DEFAULT 0.0,
    metadata JSONB
);

-- Billing
CREATE TABLE subscriptions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    user_id UUID REFERENCES users(id),
    plan_name VARCHAR(100) NOT NULL,
    status VARCHAR(20) DEFAULT 'active',
    billing_cycle VARCHAR(20) NOT NULL,
    amount_egp DECIMAL(10,2) NOT NULL,
    started_at TIMESTAMP DEFAULT NOW(),
    next_billing_date TIMESTAMP,
    paymob_subscription_id VARCHAR(255)
);
```

#### Acceptance Criteria:
- [ ] All tables created with proper foreign key constraints
- [ ] Indexes created for query performance (user_id, timestamp columns)
- [ ] Database migrations work with zero downtime
- [ ] Connection pooling configured (max 100 connections)
- [ ] Backup strategy implemented (daily automated backups)
- [ ] Data retention policies implemented (GDPR compliance)

#### Tasks:
1. Set up PostgreSQL cluster with read replicas
2. Create migration system using Alembic
3. Implement database connection pooling
4. Set up automated backup to AWS S3
5. Create database monitoring dashboard
6. Implement data archival for old usage logs

### 2.2 Neo4j Knowledge Graph
**Requirement**: Graph database for textbook knowledge representation
**Priority**: Critical

#### Graph Schema:
```cypher
// Node Types
CREATE CONSTRAINT concept_id IF NOT EXISTS FOR (c:Concept) REQUIRE c.id IS UNIQUE;
CREATE CONSTRAINT chapter_id IF NOT EXISTS FOR (ch:Chapter) REQUIRE ch.id IS UNIQUE;
CREATE CONSTRAINT textbook_id IF NOT EXISTS FOR (t:Textbook) REQUIRE t.id IS UNIQUE;

// Relationships
(:Concept)-[:PART_OF]->(:Chapter)
(:Chapter)-[:BELONGS_TO]->(:Textbook)
(:Concept)-[:PREREQUISITE]->(:Concept)
(:Concept)-[:RELATED_TO]->(:Concept)
```

#### Acceptance Criteria:
- [ ] Graph can store 1M+ nodes without performance degradation
- [ ] Cypher queries complete in <2 seconds for 3-hop traversals
- [ ] Graph backup and restore procedures working
- [ ] APOC plugins installed and configured
- [ ] Graph visualization working via Neo4j Bloom
- [ ] Automated graph health checks in place

#### Tasks:
1. Deploy Neo4j cluster in Cairo region
2. Install APOC and graph data science plugins
3. Create graph ingestion pipeline
4. Set up monitoring for graph queries
5. Implement graph backup strategy
6. Create graph visualization interface

### 2.3 Vector Database (Weaviate)
**Requirement**: Semantic search for Arabic and English text
**Priority**: Critical

#### Configuration:
```yaml
# Weaviate configuration
vectorizer: text2vec-transformers
model: sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
distance_metric: cosine
vector_dimensions: 384
```

#### Acceptance Criteria:
- [ ] Arabic and English text vectorization working
- [ ] Search queries return results in <500ms for 95th percentile
- [ ] Vector similarity scores are accurate (manual validation)
- [ ] Backup and restore procedures implemented
- [ ] Horizontal scaling capability verified
- [ ] Memory usage optimized for production load

#### Tasks:
1. Deploy Weaviate cluster
2. Configure Arabic language support
3. Implement text chunking strategy
4. Set up vector indexing pipeline
5. Create search API endpoints
6. Implement vector database monitoring

---

## 3. AI/ML Pipeline Requirements

### 3.1 LLM Integration
**Requirement**: Multi-model LLM routing with cost optimization
**Priority**: Critical

#### Technical Specifications:
