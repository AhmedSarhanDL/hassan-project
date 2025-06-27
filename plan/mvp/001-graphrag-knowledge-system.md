# GraphRAG Knowledge System MVP

## Goal
Build a minimum viable product (MVP) for a knowledge system that combines a Neo4j knowledge graph with Weaviate vector search for semantic retrieval of textbook content.

## Features
- Ingest textbook PDFs and extract chapters, concepts, and exercises
- Populate Neo4j with nodes (Textbook, Chapter, Concept) and relationships (PART_OF, PREREQUISITE, RELATED_TO)
- Chunk and vectorize content for Weaviate
- Hybrid search: semantic + graph traversal
- Simple API for querying by concept, chapter, or semantic similarity

## Architecture
- Python backend (FastAPI)
- Neo4j for knowledge graph
- Weaviate for vector search
- PostgreSQL for user/session data

## Acceptance Criteria
- [ ] Ingest at least 1 textbook (Arabic)
- [ ] Graph contains >100 concepts and relationships
- [ ] Semantic search returns relevant chunks
- [ ] API returns hybrid search results

## Tasks
1. Set up Neo4j and Weaviate locally
2. Implement PDF ingestion and parsing
3. Extract and normalize concepts (Arabic NLP)
4. Populate graph and vector DB
5. Build API endpoints for search
6. Test with sample queries

## See Also
- [GraphRAG Pipeline](../../../docs/03-ai-ml/GRAPH_RAG.md)
- [Neo4j Schema](../../../docs/02-database/NEO4J_SCHEMA.md)
- [Weaviate Schema](../../../docs/02-database/WEAVIATE_SCHEMA.md)

