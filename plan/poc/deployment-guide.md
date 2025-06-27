# POC Deployment Guide

## Overview
This guide describes how to deploy the POC locally using Docker Compose.

## Prerequisites
- Docker and Docker Compose installed
- Python 3.11+
- Node.js 18+

## Steps
1. Clone the repository
2. Copy `.env.example` to `.env` and update secrets
3. Run `docker-compose up --build`
4. Access backend at http://localhost:8000 and frontend at http://localhost:3000

## Troubleshooting
- Check logs with `docker-compose logs`
- Ensure ports 8000, 3000 are free
- For DB issues, check connection strings in `.env`

## See Also
- [Local Setup](../../../docs/07-dev-environment/LOCAL_SETUP.md)

