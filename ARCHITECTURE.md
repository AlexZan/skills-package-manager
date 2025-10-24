# Skills Package Manager - Architecture

## System Overview

```
┌─────────────────────────────────────────────────────────────┐
│                   Application Layer                          │
│  (Roundtable CLI, Web UI, Mobile App, etc.)                 │
└────────────────┬────────────────────────────────────────────┘
                 │
┌────────────────▼────────────────────────────────────────────┐
│              Skill Manager Client                            │
│  (Discovery, Installation, Loading, Caching)               │
└────────────────┬────────────────────────────────────────────┘
                 │
        ┌────────┴─────────┐
        │                  │
┌───────▼──────┐    ┌──────▼──────────┐
│  Local Cache │    │  Registry API   │
│              │    │                 │
│  .roundtable/│    │  • Search       │
│   skills/    │    │  • Metadata     │
│              │    │  • Downloads    │
└──────────────┘    └──────┬──────────┘
                           │
                    ┌──────▼──────────┐
                    │  Package Store  │
                    │  (Cloud Storage)│
                    └─────────────────┘
```

## Core Components

### 1. Skill Package Format

**Universal format** that works with multiple LLM skill systems. Based on Anthropic's Claude Skills but extensible.

**Structure:**
```
skill-name-1.0.0.spm (Skill Package file)
├── SKILL.md                   # Universal metadata (Claude Skills compatible)
├── skill.yaml                 # Extended metadata (for versioning, dependencies)
├── src/
│   ├── skill.ts              # TypeScript implementation (if applicable)
│   ├── instructions.md       # Claude-compatible instructions
│   ├── agent.ts              # Agent definition (if applicable)
│   └── types.ts              # TypeScript types (if applicable)
├── tests/
│   └── skill.test.ts         # Tests
├── docs/
│   ├── README.md
│   └── EXAMPLES.md
├── .skill/
│   ├── openai.json           # OpenAI tools format (if provided)
│   └── anthropic.json        # Anthropic metadata (optional)
└── LICENSE
```

**Primary Metadata (SKILL.md - Claude Skills Compatible):**
```markdown
---
name: data-analysis-expert
description: Expert system for data analysis and visualization
version: 1.0.0
author: roundtable-community
---

## Instructions

[Claude skill instructions per Anthropic's standard]
[This section is used directly by Claude]
```

**Extended Metadata (skill.yaml - Package Manager Standard):**
```yaml
# Universal skill metadata
name: data-analysis-expert
version: 1.0.0
description: Expert for data analysis and visualization tasks
author: roundtable-community
license: MIT
repository: https://github.com/roundtable-community/data-analysis-expert
homepage: https://github.com/roundtable-community/data-analysis-expert

# Tagging and discovery
tags:
  - data-science
  - analytics
  - visualization
capabilities:
  - data-loading
  - analysis
  - visualization

# Framework compatibility
frameworks:
  - name: anthropic-claude
    compatibility: ">=2.0.0"
    instructions-file: SKILL.md
  - name: openai
    compatibility: ">=1.0.0"
    instructions-file: .skill/openai.json
  - name: roundtable
    compatibility: ">=1.0.0"

# Dependencies
dependencies:
  roundtable: ">=1.0.0"
  python:
    pandas: ">=1.0.0"

# Package verification
checksum: sha256:abc123...
signing:
  algorithm: gpg
  public-key-id: "0x1234ABCD"
```

**Framework Adapters:**

Different LLM frameworks use different formats. The package includes adapters:

- **Claude Skills:** Native SKILL.md (Anthropic standard)
- **OpenAI:** JSON schema in `.skill/openai.json`
- **Google Vertex:** JSON schema in `.skill/google.json`
- **Roundtable:** TypeScript implementation in `src/`

The system automatically detects which framework is requesting the skill and provides the appropriate format.

### 2. Registry Service API

**Endpoints:**

```
GET /api/v1/skills
  Query params: search, tags, sort, limit, offset
  Returns: List of skill metadata

GET /api/v1/skills/:name
  Returns: Full skill metadata and versions

GET /api/v1/skills/:name/:version
  Returns: Download URL for specific version

POST /api/v1/skills (requires auth)
  Publish new skill version

GET /api/v1/search
  Query: q=<query>
  Returns: Search results with relevance ranking

GET /api/v1/tags
  Returns: Available tags/categories

GET /api/v1/health
  Returns: Registry health and stats
```

**Response Format:**
```json
{
  "name": "data-analysis-expert",
  "version": "1.0.0",
  "description": "Expert for data analysis",
  "author": "roundtable-community",
  "tags": ["data-science", "analytics"],
  "downloads": 1234,
  "rating": 4.8,
  "createdAt": "2025-01-15T10:30:00Z",
  "updatedAt": "2025-01-20T14:22:00Z",
  "versions": ["1.0.0", "0.9.0", "0.8.0"],
  "downloadUrl": "https://registry.roundtable.dev/packages/data-analysis-expert-1.0.0.spm",
  "checksum": "sha256:abc123...",
  "homepage": "https://github.com/...",
  "dependencies": {
    "roundtable": ">=2.0.0"
  }
}
```

### 3. Client/Manager API

**TypeScript Interface:**
```typescript
interface SkillManager {
  // Discovery
  search(query: string, options?: SearchOptions): Promise<SkillMetadata[]>;
  getTags(): Promise<string[]>;
  getSkill(name: string, version?: string): Promise<SkillMetadata>;

  // Installation
  install(spec: string, options?: InstallOptions): Promise<SkillPackage>;
  uninstall(name: string): Promise<void>;
  update(name: string): Promise<SkillPackage>;

  // Management
  list(): Promise<InstalledSkill[]>;
  getInstalled(name: string): Promise<InstalledSkill>;

  // Loading
  load(name: string): Promise<Skill>;
  loadAll(): Promise<Skill[]>;

  // Cache
  clearCache(): Promise<void>;
  getCacheSize(): Promise<number>;
}

interface InstallOptions {
  version?: string;           // Specific version
  registry?: string;          // Override registry
  force?: boolean;            // Force reinstall
  offline?: boolean;          // Use cache only
}

interface SearchOptions {
  tags?: string[];
  limit?: number;
  offset?: number;
  sort?: 'downloads' | 'rating' | 'recent';
}
```

### 4. Local Cache Structure

```
.roundtable/skills/
├── index.json                    # Cache metadata
├── data-analysis-expert/
│   ├── 1.0.0/
│   │   ├── skill.yaml
│   │   ├── src/
│   │   └── package.json
│   └── 0.9.0/
│       └── ...
├── web-development-panel/
│   └── 2.1.0/
│       └── ...
└── .cache/
    └── registry-metadata.json   # Last sync time, etags, etc.
```

**index.json:**
```json
{
  "installedSkills": [
    {
      "name": "data-analysis-expert",
      "version": "1.0.0",
      "installedAt": "2025-01-20T14:22:00Z",
      "source": "https://registry.roundtable.dev",
      "checksum": "sha256:abc123..."
    }
  ],
  "lastSyncTime": "2025-01-20T15:00:00Z"
}
```

### 5. Security Layer

**Mechanisms:**
- **Package Signing:** Publisher signs packages with GPG key
- **Checksum Verification:** Validate package integrity on download
- **Registry Authentication:** Token-based auth for private registries
- **Dependency Auditing:** Check for vulnerable dependencies
- **Sandbox Isolation:** Load untrusted skills in isolated context

**Key Management:**
```
Registry Public Key → Stored in Client
Developer Private Key → Signs published packages
Client Verifies → Package signature matches registry public key
```

## Data Flow

### Installation Flow

```
User: roundtable skills install data-analysis-expert

1. Client parses "data-analysis-expert" specification
2. Client queries Registry API: GET /api/v1/skills/data-analysis-expert
3. Registry returns metadata (version, dependencies, download URL)
4. Client resolves dependencies (downloads required versions)
5. Client verifies checksums and signatures
6. Client downloads .spm file from package store
7. Client extracts to .roundtable/skills/data-analysis-expert/1.0.0/
8. Client updates .roundtable/skills/index.json
9. Done! Skill ready to load at runtime
```

### Discovery Flow

```
User: roundtable skills search "data analysis"

1. Client queries Registry API: GET /api/v1/search?q=data+analysis
2. Registry searches skill metadata and returns matches
3. Client displays results with descriptions, ratings, download counts
4. User can install directly from results
```

### Loading Flow

```
Application startup:

1. Client loads .roundtable/skills/index.json
2. For each installed skill:
   a. Client loads skill.yaml metadata
   b. Client loads src/skill.ts module
   c. Client instantiates skill with context
3. Skills available to application
4. If skill load fails, log warning (non-blocking)
```

## Deployment Architecture

### Public Registry

```
┌─────────────────────────────────────┐
│  Registry Service (Node.js/API)     │
│  • Hosted on AWS/Azure/GCP          │
│  • Auto-scaling                     │
│  • CDN for package delivery         │
└──────────────┬──────────────────────┘
               │
    ┌──────────┼──────────┐
    │          │          │
┌───▼──┐  ┌───▼──┐  ┌────▼───┐
│  DB  │  │Cache │  │Storage  │
│(PSQL)│  │(Redis)│  │(S3/GCS) │
└──────┘  └──────┘  └────────┘
```

### Private Registry (Enterprise)

```
┌──────────────────────────────────────┐
│  Private Registry (Self-Hosted)      │
│  • Docker container                  │
│  • Same API as public                │
│  • Internal authentication (OAuth2)  │
└──────────────┬───────────────────────┘
               │
    ┌──────────┼──────────┐
    │          │          │
┌───▼──┐  ┌───▼──┐  ┌────▼───┐
│  DB  │  │Cache │  │Storage  │
└──────┘  └──────┘  └────────┘
```

## Implementation Technology

- **Package Format:** TAR.GZ or ZIP with standard structure
- **Registry:** Node.js/Express + PostgreSQL
- **Client:** TypeScript/Node.js
- **Storage:** S3/GCS for packages
- **Authentication:** JWT tokens
- **Package Signing:** OpenPGP (GPG)
- **Client Cache:** File-based (JSON + filesystem)

## Future Enhancements

- **Binary Packages:** Compiled skills for performance
- **Monorepo Support:** Manage multiple skills in one repo
- **CI/CD Integration:** Automated skill publishing
- **Package Benchmarks:** Performance metrics
- **Skill Composition:** Combine multiple skills
- **Marketplace UI:** Web interface for skill discovery
