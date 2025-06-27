# Content Ingestion Pipeline POC

## Goal
Build a POC for ingesting textbook content into the knowledge graph and vector DB.

## Features
- PDF ingestion and OCR
- Concept extraction (Arabic NLP)
- Populate Neo4j and Weaviate
- Simple API for ingestion

## Acceptance Criteria
- [ ] Ingest at least 1 sample textbook
- [ ] Concepts and relationships created in Neo4j
- [ ] Content chunked and vectorized in Weaviate

## Tasks
1. Implement PDF ingestion script
2. Extract concepts using Arabic NLP
3. Populate Neo4j and Weaviate
4. Build ingestion API endpoint

## See Also
- [GraphRAG Pipeline](../../../docs/03-ai-ml/GRAPH_RAG.md)
- [Neo4j Schema](../../../docs/02-database/NEO4J_SCHEMA.md)

