# Neo4j Knowledge Graph Schema

This document defines the Neo4j graph database schema for textbook knowledge representation and educational content relationships.

## Overview

Neo4j serves as our knowledge graph database for:
- Educational content structure (textbooks, chapters, concepts)
- Concept relationships and prerequisites
- Knowledge paths for learning recommendations
- Cross-subject knowledge connections

## Graph Schema Design

### Node Types

```cypher
// Node constraints for data integrity
CREATE CONSTRAINT concept_id IF NOT EXISTS FOR (c:Concept) REQUIRE c.id IS UNIQUE;
CREATE CONSTRAINT chapter_id IF NOT EXISTS FOR (ch:Chapter) REQUIRE ch.id IS UNIQUE;
CREATE CONSTRAINT textbook_id IF NOT EXISTS FOR (t:Textbook) REQUIRE t.id IS UNIQUE;
```

### Core Node Properties

#### Concept Node
```cypher
(:Concept {
  id: "concept_001",
  name: "Linear Equations",
  name_ar: "المعادلات الخطية",
  difficulty: 3,
  subject: "Mathematics",
  grade: 9,
  description: "Basic linear equations with one variable",
  description_ar: "المعادلات الخطية بمتغير واحد",
  mastery_threshold: 0.8
})
```

#### Chapter Node
```cypher
(:Chapter {
  id: "chapter_001",
  name: "Algebra Fundamentals",
  name_ar: "أساسيات الجبر",
  number: 1,
  textbook_id: "math_grade9",
  concepts_count: 15
})
```

#### Textbook Node
```cypher
(:Textbook {
  id: "math_grade9",
  title: "Mathematics Grade 9",
  title_ar: "الرياضيات الصف التاسع",
  grade: 9,
  subject: "Mathematics",
  publisher: "Ministry of Education",
  academic_year: "2024",
  language: "ar"
})
```

## Relationship Types

### Core Relationships

```cypher
// Content hierarchy
(:Concept)-[:PART_OF]->(:Chapter)
(:Chapter)-[:BELONGS_TO]->(:Textbook)

// Learning dependencies
(:Concept)-[:PREREQUISITE]->(:Concept)
(:Concept)-[:RELATED_TO {strength: 0.8}]->(:Concept)

// Cross-subject connections
(:Concept)-[:APPLIED_IN]->(:Concept)
(:Concept)-[:BUILDS_ON]->(:Concept)
```

### Advanced Relationships

```cypher
// Difficulty progression
(:Concept)-[:EASIER_THAN]->(:Concept)
(:Concept)-[:HARDER_THAN]->(:Concept)

// Learning paths
(:Concept)-[:NEXT_SUGGESTED]->(:Concept)
(:Concept)-[:ALTERNATIVE_TO]->(:Concept)
```

## Query Examples

### Find Prerequisites for a Concept
```cypher
MATCH (target:Concept {name: "Quadratic Equations"})
MATCH (prereq:Concept)-[:PREREQUISITE]->(target)
RETURN prereq.name, prereq.difficulty
ORDER BY prereq.difficulty
```

### Get Learning Path
```cypher
MATCH path = (start:Concept {name: "Basic Arithmetic"})-[:PREREQUISITE*1..3]->(end:Concept {name: "Calculus"})
RETURN [node in nodes(path) | node.name] AS learning_path
```

### Cross-Subject Connections
```cypher
MATCH (math:Concept {subject: "Mathematics"})-[:APPLIED_IN]->(physics:Concept {subject: "Physics"})
RETURN math.name, physics.name, physics.grade
```

## Performance Requirements

### Indexing Strategy
```cypher
// Performance indexes
CREATE INDEX concept_subject_grade IF NOT EXISTS FOR (c:Concept) ON (c.subject, c.grade);
CREATE INDEX concept_difficulty IF NOT EXISTS FOR (c:Concept) ON (c.difficulty);
CREATE INDEX textbook_grade_subject IF NOT EXISTS FOR (t:Textbook) ON (t.grade, t.subject);
```

### Query Performance Targets
- Graph can store 1M+ nodes without performance degradation
- Cypher queries complete in <2 seconds for 3-hop traversals
- Path finding queries return results in <1 second

## Acceptance Criteria

- [ ] Graph can store 1M+ nodes without performance degradation
- [ ] Cypher queries complete in <2 seconds for 3-hop traversals
- [ ] Graph backup and restore procedures working
- [ ] APOC plugins installed and configured
- [ ] Graph visualization working via Neo4j Bloom
- [ ] Automated graph health checks in place

## Implementation Tasks

1. Deploy Neo4j cluster in Cairo region
2. Install APOC and graph data science plugins
3. Create graph ingestion pipeline
4. Set up monitoring for graph queries
5. Implement graph backup strategy
6. Create graph visualization interface

## Graph Ingestion Pipeline

### Content Processing Flow
1. **OCR Extraction**: Convert textbook PDFs to structured text
2. **Concept Extraction**: Identify concepts using NLP
3. **Relationship Mapping**: Determine concept relationships
4. **Graph Population**: Create nodes and edges in Neo4j
5. **Validation**: Verify graph integrity and relationships

## See Also
- [Graph RAG Pipeline](../03-ai-ml/GRAPH_RAG.md)
- [Content Ingestion](../03-ai-ml/GRAPH_RAG.md#content-ingestion)
- [Backup Procedures](../09-operations/BACKUP_RESTORE.md)

