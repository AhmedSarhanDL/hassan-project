# Backup & Restore Procedures

This document describes backup and restore procedures for the Egypt Edututor platform databases and services.

## Overview

Backups ensure:
- Data recovery in case of failure or corruption
- Compliance with data retention policies
- Minimal downtime during restore

## Databases

### PostgreSQL
- Daily automated backups to AWS S3
- Retain 30 days of backups
- Use `pg_dump` for logical backups
- Restore with `pg_restore` or `psql`

### Neo4j
- Daily full database dumps
- Retain 14 days of backups
- Use `neo4j-admin dump` and `neo4j-admin restore`

### Weaviate
- Use built-in backup to S3
- Retain 7 days of backups
- Restore via Weaviate admin API

## Restore Procedures
- Documented step-by-step for each DB
- Test restores monthly
- Notify stakeholders on restore events

## Acceptance Criteria
- [ ] All DBs have automated backups
- [ ] Restore procedures tested monthly
- [ ] Backup retention meets compliance

## See Also
- [Migrations](../../02-database/MIGRATIONS.md)
- [SRE Dashboards](SRE_DASHBOARDS.md)

