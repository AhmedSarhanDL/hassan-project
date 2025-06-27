# Local Development Setup

This document describes how to set up a local development environment for the Egypt Edututor platform.

## Prerequisites
- Python 3.11+
- Node.js 18+
- Docker
- PostgreSQL 15+
- Neo4j 5+
- Weaviate 1.22+

## Setup Steps

### 1. Clone the Repository
```bash
git clone https://github.com/AhmedSarhanDL/hassan-project.git
cd hassan-project
```

### 2. Python Backend
```bash
cd src/poc/backend
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env  # Add secrets manually
alembic upgrade head  # Run DB migrations
```

### 3. Frontend
```bash
cd ../../frontend
npm install
npm run dev
```

### 4. Databases
- Start PostgreSQL, Neo4j, and Weaviate (see `docker-compose.yml`)
- Update connection strings in `.env`

### 5. Run Backend
```bash
cd src/poc/backend
uvicorn main:app --reload
```

### 6. Run Frontend
```bash
cd src/poc/frontend
npm run dev
```

## Troubleshooting
- Check logs in `logs/` directory
- Use `docker-compose logs` for DB containers
- Ensure ports 8000 (backend), 3000 (frontend), 7687 (Neo4j), 8080 (Weaviate) are free

## See Also
- [Code Style](CODE_STYLE.md)
- [Git Flow](GIT_FLOW.md)

