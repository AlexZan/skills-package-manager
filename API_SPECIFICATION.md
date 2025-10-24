# API Specification: Skills Registry REST API

**Version:** 1.0.0 (Phase 1)
**Status:** Draft
**Last Updated:** 2025-10-24

---

## Overview

The Skills Registry API is a RESTful API for discovering, managing, and publishing AI agent skills.

**Base URL:** `https://registry.skills.dev/api/v1` (or local: `http://localhost:3000/api/v1`)

**Authentication:** JWT Bearer tokens (for publishing, optional for reading)

**Response Format:** JSON

---

## API Endpoints Summary

| Method | Endpoint | Purpose | Auth Required |
|--------|----------|---------|---|
| GET | `/skills` | List all skills | ❌ |
| GET | `/skills/:name` | Get skill details | ❌ |
| GET | `/search` | Search skills | ❌ |
| GET | `/tags` | List available tags | ❌ |
| POST | `/skills` | Publish new skill | ✅ |
| GET | `/health` | Health check | ❌ |
| GET | `/version` | API version | ❌ |

---

## 1. GET /skills - List All Skills

**Description:** Retrieve a paginated list of all published skills.

**Request:**
```http
GET /api/v1/skills?limit=20&offset=0&sort=recent
```

**Query Parameters:**
```
limit     (optional, default: 20)      Number of results (1-100)
offset    (optional, default: 0)       Pagination offset
sort      (optional, default: recent)  Sort: recent, downloads, rating
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "skills": [
      {
        "id": "uuid-1",
        "name": "data-analyst",
        "version": "1.0.0",
        "description": "Analyzes financial data and creates reports",
        "author": {
          "name": "John Doe",
          "email": "john@example.com"
        },
        "license": "MIT",
        "keywords": ["data", "analysis", "finance"],
        "capabilities": ["financial-analysis", "visualization"],
        "frameworks": ["roundtable", "claude-api"],
        "downloads": 1234,
        "rating": 4.8,
        "homepage": "https://github.com/...",
        "repository": "https://github.com/...",
        "npmPackage": "@skills/data-analyst",
        "publishedAt": "2025-01-15T10:30:00Z",
        "updatedAt": "2025-01-20T14:22:00Z"
      }
    ],
    "pagination": {
      "limit": 20,
      "offset": 0,
      "total": 342,
      "hasMore": true
    }
  }
}
```

**Status Codes:**
- `200 OK` - Success
- `400 Bad Request` - Invalid query parameters
- `500 Internal Server Error` - Server error

---

## 2. GET /skills/:name - Get Skill Details

**Description:** Retrieve detailed information about a specific skill.

**Request:**
```http
GET /api/v1/skills/data-analyst
```

**Path Parameters:**
```
name  (required, string)  Skill name (lowercase, URL-encoded)
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "id": "uuid-1",
    "name": "data-analyst",
    "versions": [
      {
        "version": "1.0.0",
        "description": "Analyzes financial data and creates reports",
        "author": {
          "name": "John Doe",
          "email": "john@example.com"
        },
        "license": "MIT",
        "keywords": ["data", "analysis", "finance"],
        "capabilities": ["financial-analysis", "visualization"],
        "frameworks": {
          "roundtable": ">=1.0.0",
          "claude-api": ">=1.0.0"
        },
        "dependencies": {
          "roundtable": ">=1.0.0"
        },
        "homepage": "https://github.com/...",
        "repository": "https://github.com/...",
        "npmPackage": "@skills/data-analyst",
        "npmVersion": "1.0.0",
        "checksum": "sha256:abc123def456...",
        "size": 45678,
        "publishedAt": "2025-01-15T10:30:00Z",
        "downloads": 1234,
        "rating": 4.8
      },
      {
        "version": "0.9.0",
        "description": "Previous version",
        "publishedAt": "2025-01-10T08:15:00Z",
        "downloads": 234
      }
    ],
    "totalDownloads": 1468,
    "createdAt": "2025-01-10T00:00:00Z",
    "updatedAt": "2025-01-20T14:22:00Z"
  }
}
```

**Response (404 Not Found):**
```json
{
  "success": false,
  "error": "Skill not found",
  "message": "No skill named 'data-analyst' exists"
}
```

**Status Codes:**
- `200 OK` - Success
- `404 Not Found` - Skill doesn't exist
- `500 Internal Server Error` - Server error

---

## 3. GET /search - Search Skills

**Description:** Search for skills by keyword, tags, or capabilities.

**Request:**
```http
GET /api/v1/search?q=data%20analysis&tags=data,analysis&capabilities=visualization&frameworks=claude-api&limit=10
```

**Query Parameters:**
```
q              (optional, string)        Search query (name, description, tags)
tags           (optional, string[])      CSV list of tags to filter
capabilities   (optional, string[])      CSV list of capabilities to filter
frameworks     (optional, string[])      CSV list of frameworks to filter
limit          (optional, default: 20)   Number of results (1-100)
offset         (optional, default: 0)    Pagination offset
sort           (optional, default: relevant) Sort: relevant, recent, downloads, rating
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": "uuid-1",
        "name": "data-analyst",
        "version": "1.0.0",
        "description": "Analyzes financial data and creates reports",
        "author": {
          "name": "John Doe",
          "email": "john@example.com"
        },
        "keywords": ["data", "analysis", "finance"],
        "capabilities": ["financial-analysis", "visualization"],
        "frameworks": ["roundtable", "claude-api"],
        "downloads": 1234,
        "rating": 4.8,
        "matchScore": 0.95,
        "matchReasons": ["keyword match: data", "keyword match: analysis", "capability match: visualization"]
      }
    ],
    "pagination": {
      "limit": 10,
      "offset": 0,
      "total": 15,
      "hasMore": true
    },
    "query": {
      "q": "data analysis",
      "tags": ["data", "analysis"],
      "capabilities": ["visualization"],
      "frameworks": ["claude-api"]
    }
  }
}
```

**Status Codes:**
- `200 OK` - Success
- `400 Bad Request` - Invalid query parameters
- `500 Internal Server Error` - Server error

---

## 4. GET /tags - List Available Tags

**Description:** Get a list of all available tags with count of skills using each.

**Request:**
```http
GET /api/v1/tags?sort=count
```

**Query Parameters:**
```
sort  (optional, default: alpha)  Sort: alpha (alphabetical) or count (by skill count)
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "tags": [
      {
        "tag": "data",
        "count": 45,
        "description": "Data analysis and processing skills"
      },
      {
        "tag": "analysis",
        "count": 38,
        "description": "Analytical skills for various domains"
      },
      {
        "tag": "visualization",
        "count": 32,
        "description": "Visualization and chart generation"
      }
    ],
    "total": 156
  }
}
```

**Status Codes:**
- `200 OK` - Success
- `500 Internal Server Error` - Server error

---

## 5. POST /skills - Publish New Skill

**Description:** Publish a new skill or new version of existing skill to the registry.

**Authentication Required:** Yes (JWT Bearer token)

**Request:**
```http
POST /api/v1/skills
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "name": "data-analyst",
  "version": "1.0.0",
  "description": "Analyzes financial data and creates reports",
  "author": {
    "name": "John Doe",
    "email": "john@example.com"
  },
  "license": "MIT",
  "keywords": ["data", "analysis", "finance"],
  "capabilities": ["financial-analysis", "visualization"],
  "frameworks": {
    "roundtable": ">=1.0.0",
    "claude-api": ">=1.0.0"
  },
  "dependencies": {
    "roundtable": ">=1.0.0"
  },
  "homepage": "https://github.com/yourname/data-analyst",
  "repository": "https://github.com/yourname/data-analyst",
  "npmPackage": "@skills/data-analyst",
  "npmVersion": "1.0.0",
  "skillYamlUrl": "https://raw.githubusercontent.com/yourname/data-analyst/main/skill.yaml",
  "skillMdUrl": "https://raw.githubusercontent.com/yourname/data-analyst/main/SKILL.md",
  "checksum": "sha256:abc123def456...",
  "signature": "pgp_signature_base64_encoded..."
}
```

**Request Body (Required Fields):**
```
name             (string)           Skill name (lowercase, a-z0-9-)
version          (string)           Semantic version (e.g., 1.0.0)
description      (string)           Brief description (1-200 chars)
author           (object)           Author info
  name           (string)           Author name
  email          (string)           Author email
license          (string)           License type (MIT, Apache-2.0, etc.)
keywords         (array<string>)    Tags for discovery (1-10)
capabilities     (array<string>)    What the skill can do (1-10)
frameworks       (object)           Framework compatibility
  [framework]    (string)           Semantic version requirement
```

**Request Body (Optional Fields):**
```
dependencies     (object)           External dependencies
homepage         (string)           Project homepage URL
repository       (string)           Repository URL
npmPackage       (string)           npm package name (e.g., @skills/xyz)
npmVersion       (string)           npm package version
skillYamlUrl     (string)           URL to skill.yaml file
skillMdUrl       (string)           URL to SKILL.md file
checksum         (string)           SHA256 checksum for verification
signature        (string)           GPG signature (base64-encoded)
```

**Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "id": "uuid-1",
    "name": "data-analyst",
    "version": "1.0.0",
    "message": "Skill published successfully",
    "url": "https://registry.skills.dev/api/v1/skills/data-analyst",
    "npmUrl": "https://www.npmjs.com/package/@skills/data-analyst",
    "publishedAt": "2025-01-20T14:22:00Z"
  }
}
```

**Response (400 Bad Request):**
```json
{
  "success": false,
  "error": "Validation failed",
  "fields": {
    "name": ["Name must be lowercase and contain only a-z0-9-"],
    "version": ["Version must follow semantic versioning"],
    "description": ["Description must be 1-200 characters"]
  }
}
```

**Response (409 Conflict):**
```json
{
  "success": false,
  "error": "Version already exists",
  "message": "Skill 'data-analyst' version '1.0.0' already published"
}
```

**Status Codes:**
- `201 Created` - Success
- `400 Bad Request` - Validation failed
- `401 Unauthorized` - Missing or invalid token
- `409 Conflict` - Version already exists
- `500 Internal Server Error` - Server error

**Security Notes:**
- JWT token required
- Rate limiting: 10 publishes per hour per user
- Checksum verification required
- Signature validation recommended

---

## 6. GET /health - Health Check

**Description:** Check if the registry service is healthy.

**Request:**
```http
GET /api/v1/health
```

**Response (200 OK):**
```json
{
  "success": true,
  "status": "healthy",
  "data": {
    "api": "ok",
    "database": "ok",
    "timestamp": "2025-01-20T14:22:00Z",
    "uptime": 86400
  }
}
```

**Status Codes:**
- `200 OK` - All systems operational
- `503 Service Unavailable` - Service degraded

---

## 7. GET /version - API Version

**Description:** Get the current API version.

**Request:**
```http
GET /api/v1/version
```

**Response (200 OK):**
```json
{
  "success": true,
  "data": {
    "api": "1.0.0",
    "status": "stable",
    "buildDate": "2025-01-15",
    "environment": "production"
  }
}
```

---

## Error Responses

### Standard Error Format

All errors follow this format:

```json
{
  "success": false,
  "error": "Error type",
  "message": "Human-readable error message",
  "details": {
    "field": ["Specific validation error"]
  },
  "timestamp": "2025-01-20T14:22:00Z",
  "requestId": "req-123456789"
}
```

### Error Types

| Code | Status | Meaning |
|------|--------|---------|
| `VALIDATION_ERROR` | 400 | Input validation failed |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication |
| `FORBIDDEN` | 403 | User not allowed to perform action |
| `NOT_FOUND` | 404 | Resource not found |
| `CONFLICT` | 409 | Resource already exists |
| `RATE_LIMIT` | 429 | Too many requests |
| `SERVER_ERROR` | 500 | Internal server error |
| `SERVICE_UNAVAILABLE` | 503 | Service temporarily unavailable |

---

## Authentication

### JWT Bearer Token

For endpoints requiring authentication, pass the token in the Authorization header:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Token Structure:**
```
Header:
{
  "alg": "HS256",
  "typ": "JWT"
}

Payload:
{
  "sub": "user_id",
  "name": "User Name",
  "email": "user@example.com",
  "iat": 1516239022,
  "exp": 1516325422
}

Signature:
HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

**Token Endpoints (Phase 2):**
- `POST /auth/register` - Register new user
- `POST /auth/login` - Get JWT token
- `POST /auth/refresh` - Refresh expired token

---

## Rate Limiting

**Rate Limits (Phase 1):**
- Read endpoints: 100 requests/minute per IP
- Publish endpoint: 10 requests/hour per user
- Search endpoint: 30 requests/minute per IP

**Headers Returned:**
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 99
X-RateLimit-Reset: 1516325422
```

---

## Pagination

**Pagination Format:**
```json
{
  "pagination": {
    "limit": 20,
    "offset": 0,
    "total": 342,
    "hasMore": true
  }
}
```

**Parameters:**
- `limit`: Results per page (default: 20, max: 100)
- `offset`: Number of results to skip (default: 0)
- `total`: Total number of results
- `hasMore`: Whether more results available

**Examples:**
```
GET /api/v1/skills?limit=20&offset=0      # First 20
GET /api/v1/skills?limit=20&offset=20     # Next 20
GET /api/v1/skills?limit=20&offset=40     # Next 20
```

---

## Data Types

### Author Object
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "url": "https://example.com"
}
```

### Skill Object (Minimal)
```json
{
  "id": "uuid",
  "name": "skill-name",
  "version": "1.0.0",
  "description": "Short description",
  "author": { "name": "...", "email": "..." },
  "keywords": ["tag1", "tag2"],
  "capabilities": ["cap1", "cap2"],
  "frameworks": ["roundtable", "claude-api"],
  "downloads": 1234,
  "rating": 4.8,
  "publishedAt": "2025-01-15T10:30:00Z"
}
```

### Skill Object (Full)
```json
{
  "id": "uuid",
  "name": "skill-name",
  "version": "1.0.0",
  "description": "Full description",
  "author": { "name": "...", "email": "..." },
  "license": "MIT",
  "keywords": ["tag1", "tag2"],
  "capabilities": ["cap1", "cap2"],
  "frameworks": {
    "roundtable": ">=1.0.0",
    "claude-api": ">=1.0.0"
  },
  "dependencies": {
    "roundtable": ">=1.0.0"
  },
  "homepage": "https://...",
  "repository": "https://...",
  "npmPackage": "@skills/xyz",
  "npmVersion": "1.0.0",
  "checksum": "sha256:...",
  "size": 45678,
  "downloads": 1234,
  "rating": 4.8,
  "publishedAt": "2025-01-15T10:30:00Z",
  "updatedAt": "2025-01-20T14:22:00Z",
  "createdAt": "2025-01-10T00:00:00Z"
}
```

---

## Best Practices

1. **Always check `success` field** - Indicates request success/failure
2. **Include `requestId` in logs** - For debugging and support
3. **Implement exponential backoff** - For retrying failed requests
4. **Cache GET responses** - Reduce load and improve UX
5. **Validate input** - Before sending to API
6. **Use semantic versioning** - For skill versions
7. **Include checksum** - For package integrity verification
8. **Sign packages** - Using GPG for security (Phase 1.5)

---

## CORS

**Allowed Origins (Phase 1):**
- `*` (all origins)

**Allowed Methods:**
- GET, POST, OPTIONS

**Allowed Headers:**
- Content-Type
- Authorization

**Phase 2:** Restrict to specific origins

---

## Versioning

**API Version:** 1.0.0 (Phase 1)

**Versioning Strategy:**
- URL-based: `/api/v1/`, `/api/v2/`, etc.
- Breaking changes = new version
- Non-breaking changes = patch version

**Deprecation Policy:**
- Old versions supported for 12 months
- Advance notice for deprecation
- Migration guide provided

---

## Examples

### Example 1: Search for Data Analysis Skills

```bash
curl -X GET "https://registry.skills.dev/api/v1/search?q=data&capabilities=visualization" \
  -H "Content-Type: application/json"
```

### Example 2: Get Skill Details

```bash
curl -X GET "https://registry.skills.dev/api/v1/skills/data-analyst" \
  -H "Content-Type: application/json"
```

### Example 3: Publish a New Skill

```bash
curl -X POST "https://registry.skills.dev/api/v1/skills" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer YOUR_JWT_TOKEN" \
  -d '{
    "name": "data-analyst",
    "version": "1.0.0",
    "description": "Analyzes financial data",
    "author": {"name": "John Doe", "email": "john@example.com"},
    "license": "MIT",
    "keywords": ["data", "analysis"],
    "capabilities": ["financial-analysis"],
    "frameworks": {"roundtable": ">=1.0.0"}
  }'
```

---

## OpenAPI 3.0 Definition

Full OpenAPI 3.0 specification available at:
- `https://registry.skills.dev/api/v1/openapi.json`
- `https://registry.skills.dev/api/v1/docs` (Swagger UI)

---

**Status:** Draft (Ready for Implementation)
**Next:** Database Schema Specification
**Date:** 2025-10-24
