# REST API OpenAPI Specification

This document defines the REST API specifications and conventions for the Egypt Edututor platform.

## Overview

The platform uses OpenAPI 3.0 specification for all REST endpoints with:
- Consistent error handling and response formats
- Comprehensive request/response validation
- Authentication and authorization schemas
- Rate limiting and quota management

## OpenAPI Schema

### Base Configuration

```yaml
openapi: 3.0.3
info:
  title: Egypt Edututor API
  description: AI-powered tutoring platform for Egyptian K-12 students
  version: 1.0.0
  contact:
    name: Egypt Edututor API Support
    email: api-support@edututor.eg
  license:
    name: MIT
    url: https://opensource.org/licenses/MIT

servers:
  - url: https://api.edututor.eg/v1
    description: Production server
  - url: https://staging-api.edututor.eg/v1
    description: Staging server

security:
  - BearerAuth: []
  - ApiKeyAuth: []
```

### Authentication Schemas

```yaml
components:
  securitySchemes:
    BearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
    ApiKeyAuth:
      type: apiKey
      in: header
      name: X-API-Key
  
  schemas:
    User:
      type: object
      required:
        - id
        - email
        - profile
      properties:
        id:
          type: string
          format: uuid
          example: "123e4567-e89b-12d3-a456-426614174000"
        email:
          type: string
          format: email
          example: "student@example.com"
        phone:
          type: string
          pattern: "^\+20[0-9]{10}$"
          example: "+201234567890"
        profile:
          $ref: '#/components/schemas/UserProfile'
    
    UserProfile:
      type: object
      properties:
        name:
          type: string
          example: "أحمد محمد"
        grade:
          type: integer
          minimum: 1
          maximum: 12
          example: 9
        subjects:
          type: array
          items:
            type: string
          example: ["Mathematics", "Physics"]
        language_preference:
          type: string
          enum: ["ar", "en", "mixed"]
          example: "ar"
```

### Chat API Endpoints

```yaml
paths:
  /chat/sessions:
    post:
      summary: Create new chat session
      description: Starts a new tutoring session with the AI tutor
      tags:
        - Chat
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - subject
                - grade
              properties:
                subject:
                  type: string
                  example: "Mathematics"
                grade:
                  type: integer
                  example: 9
                language:
                  type: string
                  enum: ["ar", "en"]
                  example: "ar"
                context:
                  type: object
                  properties:
                    chapter:
                      type: string
                      example: "Linear Equations"
                    difficulty_level:
                      type: integer
                      minimum: 1
                      maximum: 10
                      example: 5
      responses:
        '201':
          description: Session created successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  session_id:
                    type: string
                    format: uuid
                  expires_at:
                    type: string
                    format: date-time
                  estimated_cost:
                    type: number
                    format: float
                    example: 0.05
        '400':
          $ref: '#/components/responses/BadRequest'
        '401':
          $ref: '#/components/responses/Unauthorized'
        '429':
          $ref: '#/components/responses/RateLimited'

  /chat/sessions/{session_id}/messages:
    post:
      summary: Send message to AI tutor
      tags:
        - Chat
      parameters:
        - name: session_id
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - message
              properties:
                message:
                  type: string
                  example: "كيف أحل هذه المعادلة: 2x + 5 = 13؟"
                attachments:
                  type: array
                  items:
                    type: object
                    properties:
                      type:
                        type: string
                        enum: ["image", "audio"]
                      data:
                        type: string
                        format: base64
