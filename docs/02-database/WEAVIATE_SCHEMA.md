# Weaviate Vector Database Schema

This document defines the Weaviate vector database configuration for semantic search of Arabic and English educational content.

## Overview

Weaviate serves as our vector database for:
- Semantic search across textbook content
- Multi-language text vectorization (Arabic/English)
- Context retrieval for GraphRAG pipeline
- Similarity-based content recommendations

## Class Schema Configuration

### Base Configuration

```yaml
# Weaviate configuration
vectorizer: text2vec-transformers
model: sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2
distance_metric: cosine
vector_dimensions: 384
```

### Content Class Schema

```json
{
  "class": "TextChunk",
  "description": "Chunks of educational content for semantic search",
  "vectorizer": "text2vec-transformers",
  "moduleConfig": {
    "text2vec-transformers": {
      "model": "sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2",
      "options": {
        "waitForModel": true,
        "useGPU": true
      }
    }
  },
  "properties": [
    {
      "name": "content",
      "dataType": ["text"],
      "description": "The actual text content",
      "moduleConfig": {
        "text2vec-transformers": {
          "skip": false,
          "vectorizePropertyName": false
        }
      }
    },
    {
      "name": "content_ar",
      "dataType": ["text"],
      "description": "Arabic version of the content"
    },
    {
      "name": "subject",
      "dataType": ["string"],
      "description": "Subject area (Math, Physics, etc.)"
    },
    {
      "name": "grade",
      "dataType": ["int"],
      "description": "Educational grade level"
    },
    {
      "name": "chapter_id",
      "dataType": ["string"],
      "description": "Reference to Neo4j chapter node"
    },
    {
      "name": "concept_ids",
      "dataType": ["string[]"],
      "description": "Associated concept IDs from knowledge graph"
    },
    {
      "name": "chunk_index",
      "dataType": ["int"],
      "description": "Order of chunk within source document"
    },
    {
      "name": "chunk_type",
      "dataType": ["string"],
      "description": "Type: paragraph, equation, example, exercise"
    },
    {
      "name": "difficulty",
      "dataType": ["number"],
      "description": "Estimated difficulty score (1-10)"
    },
    {
      "name": "language",
      "dataType": ["string"],
      "description": "Primary language: ar, en, mixed"
    },
    {
      "name": "created_at",
      "dataType": ["date"],
      "description": "Ingestion timestamp"
    }
  ]
}
```

### Question Class Schema

```json
{
  "class": "Question",
  "description": "Practice questions and exercises",
  "vectorizer": "text2vec-transformers",
  "properties": [
    {
      "name": "question_text",
      "dataType": ["text"],
      "description": "The question content"
    },
    {
      "name": "answer_text",
      "dataType": ["text"],
      "description": "The correct answer"
    },
    {
      "name": "explanation",
      "dataType": ["text"],
      "description": "Step-by-step solution explanation"
    },
    {
      "name": "question_type",
      "dataType": ["string"],
      "description": "Type: multiple_choice, short_answer, essay, calculation"
    },
    {
      "name": "difficulty",
      "dataType": ["number"],
      "description": "Difficulty level (1-10)"
    },
    {
      "name": "concept_ids",
      "dataType": ["string[]"],
      "description": "Related concept IDs"
    },
    {
      "name": "subject",
      "dataType": ["string"],
      "description": "Subject area"
    },
    {
      "name": "grade",
      "dataType": ["int"],
      "description": "Grade level"
    }
  ]
}
```

## Vectorization Settings

### Text Chunking Strategy

```python
# Text chunking configuration
CHUNK_SIZE = 512  # tokens
CHUNK_OVERLAP = 64  # tokens
MIN_CHUNK_SIZE = 100  # minimum tokens per chunk

# Arabic-specific processing
ARABIC_NORMALIZATION = True
DIACRITIC_REMOVAL = True
LETTER_NORMALIZATION = True  # normalize different forms of alef, etc.
```

### Multi-language Support

```yaml
# Language detection and processing
languages:
  primary: ["ar", "en"]
  models:
    ar: "CAMeL-Lab/bert-base-arabic-camelbert-mix"
    en: "sentence-transformers/all-MiniLM-L6-v2"
    multilingual: "sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2"
```

## Query Examples

### Semantic Search

```python
# Basic semantic search
import weaviate

client = weaviate.Client("http://localhost:8080")

result = client.query.get("TextChunk", [
    "content", "subject", "grade", "concept_ids"
]).with_near_text({
    "concepts": ["linear equations", "المعادلات الخطية"]
