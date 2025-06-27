# Database Migrations

This document outlines the database migration strategy using Alembic for PostgreSQL schema changes and procedures for Neo4j and Weaviate updates.

## Overview

Our migration strategy ensures:
- Zero-downtime deployments for PostgreSQL
- Consistent versioning across all database systems
- Rollback capability for failed migrations
- Data integrity during schema changes

## PostgreSQL Migrations with Alembic

### Setup and Configuration

```python
# alembic.ini configuration
[alembic]
script_location = migrations
file_template = %%(year)d%%(month).2d%%(day).2d_%%(hour).2d%%(minute).2d_%%(rev)s_%%(slug)s
sqlalchemy.url = postgresql://user:pass@localhost/edututor

[loggers]
keys = root,sqlalchemy,alembic

[logger_alembic]
level = INFO
handlers = 
qualname = alembic
```

### Migration Workflow

#### 1. Creating Migrations

```bash
# Auto-generate migration from model changes
alembic revision --autogenerate -m "Add guardian consent table"

# Create empty migration for data changes
alembic revision -m "Populate default learning profiles"
```

#### 2. Migration File Structure

```python
"""Add guardian consent table

Revision ID: 20241201_1200_abc123_add_guardian_consent
Revises: 20241130_1500_def456_initial_schema
Create Date: 2024-12-01 12:00:00.000000

"""
from alembic import op
import sqlalchemy as sa

# revision identifiers
revision = '20241201_1200_abc123_add_guardian_consent'
down_revision = '20241130_1500_def456_initial_schema'
branch_labels = None
depends_on = None

def upgrade():
    op.create_table('guardian_consents',
        sa.Column('id', sa.UUID(), nullable=False, default=sa.text('gen_random_uuid()')),
        sa.Column('user_id', sa.UUID(), nullable=False),
        sa.Column('guardian_email', sa.String(length=255), nullable=False),
        sa.Column('consent_given', sa.Boolean(), nullable=False),
        sa.Column('consent_timestamp', sa.TIMESTAMP(), nullable=False, default=sa.text('NOW()')),
        sa.ForeignKeyConstraint(['user_id'], ['users.id'], ),
        sa.PrimaryKeyConstraint('id')
    )

def downgrade():
    op.drop_table('guardian_consents')
```

#### 3. Zero-Downtime Deployment Strategy

```python
# Safe migration patterns for production

# Step 1: Add new column (nullable initially)
def upgrade_step1():
    op.add_column('users', sa.Column('new_field', sa.String(100), nullable=True))

# Step 2: Populate data (separate migration)
def upgrade_step2():
    connection = op.get_bind()
    connection.execute(
        "UPDATE users SET new_field = 'default_value' WHERE new_field IS NULL"
    )

# Step 3: Make column non-nullable (separate migration)
def upgrade_step3():
    op.alter_column('users', 'new_field', nullable=False)
```

### Deployment Commands

```bash
# Check current revision
alembic current

# Show pending migrations
alembic show

# Apply all pending migrations
alembic upgrade head

# Apply specific migration
alembic upgrade abc123

# Rollback to previous version
alembic downgrade -1

# Rollback to specific revision
alembic downgrade def456
```

## Neo4j Schema Migrations

### Graph Schema Versioning

```cypher
-- Schema version tracking
CREATE CONSTRAINT schema_version_unique IF NOT EXISTS 
FOR (v:SchemaVersion) REQUIRE v.version IS UNIQUE;

-- Track schema changes
CREATE (v:SchemaVersion {
  version: "1.2.0",
  applied_at: datetime(),
  description: "Add prerequisite relationships"
});
```

### Migration Scripts

```python
# neo4j_migrations/001_add_prerequisites.py
from neo4j import GraphDatabase

def upgrade(tx):
    """Add prerequisite relationships between concepts"""
    query = """
    MATCH (prereq:Concept), (target:Concept)
    WHERE prereq.subject = target.subject 
    AND prereq.difficulty < target.difficulty
    AND prereq.grade <= target.grade
    CREATE (prereq)-[:PREREQUISITE]->(target)
    """
    tx.run(query)

def downgrade(tx):
    """Remove prerequisite relationships"""
    query = "MATCH ()-[r:PREREQUISITE]->() DELETE r"
    tx.run(query)
```

### Neo4j Migration Runner

```python
# tools/neo4j_migrate.py
import os
from neo4j import GraphDatabase

class Neo4jMigrator:
    def __init__(self, uri, user, password):
        self.driver = GraphDatabase.driver(uri, auth=(user, password))
    
    def get_current_version(self):
        with self.driver.session() as session:
            result = session.run(
                "MATCH (v:SchemaVersion) RETURN v.version ORDER BY v.applied_at DESC LIMIT 1"
            )
            record = result.single()
            return record["v.version"] if record else "0.0.0"
    
    def run_migrations(self, target_version=None):
        current = self.get_current_version()
        # Load and run pending migrations
        pass
```

## Weaviate Schema Updates

### Class Schema Versioning

```python
# weaviate_migrations/schema_v2.py
def upgrade_schema(client):
    """Add new properties to TextChunk class"""
    
    # Add new property
    property_def = {
        "name": "difficulty_score",
        "dataType": ["number"],
        "description": "Calculated difficulty score"
