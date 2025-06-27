# PostgreSQL Schema

This document defines the relational database schema for user data, billing, analytics, and core platform functionality.

## Overview

PostgreSQL serves as our primary relational database for:
- User authentication and profiles
- Guardian consent management
- Learning analytics and progress tracking
- Usage monitoring and billing
- Subscription management

## Core Schema Design

### Users and Authentication

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
```

### Learning Analytics

```sql
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
```

### Usage Tracking

```sql
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
```

### Billing and Subscriptions

```sql
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

## Performance Requirements

### Indexes
```sql
-- Performance indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_learning_profiles_user_subject ON learning_profiles(user_id, subject);
CREATE INDEX idx_usage_logs_user_timestamp ON usage_logs(user_id, timestamp);
CREATE INDEX idx_subscriptions_user_status ON subscriptions(user_id, status);
```

### Connection Pooling
- Maximum 100 connections configured
- Connection timeout: 30 seconds
- Statement timeout: 60 seconds

## Acceptance Criteria

- [ ] All tables created with proper foreign key constraints
- [ ] Indexes created for query performance (user_id, timestamp columns)
- [ ] Database migrations work with zero downtime
- [ ] Connection pooling configured (max 100 connections)
- [ ] Backup strategy implemented (daily automated backups)
- [ ] Data retention policies implemented (GDPR compliance)

## Implementation Tasks

1. Set up PostgreSQL cluster with read replicas
2. Create migration system using Alembic
3. Implement database connection pooling
4. Set up automated backup to AWS S3
5. Create database monitoring dashboard
6. Implement data archival for old usage logs

## See Also
- [Migration Guide](MIGRATIONS.md)
- [Backup & Restore](../09-operations/BACKUP_RESTORE.md)

