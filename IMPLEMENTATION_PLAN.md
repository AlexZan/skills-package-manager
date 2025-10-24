# Skills Package Manager - Phase 1 Implementation Plan

## Overview

Phase 1 goal: **Build a working skills package manager with full API, CLI, and registry - proof of concept that works with Roundtable and generic skill packages.**

Timeline: ASAP
Scope: Generic (works for any skill consumer, not just Roundtable)
Registry: Full production-ready API
Standards: Proof-of-concept first, contribute to standards later

---

## Phase 1 Deliverables

### 1. Universal Skill Package Format ✓ (Designed, needs validation)
- **SKILL.md** - Anthropic-compatible format (required)
- **skill.yaml** - Extended metadata with versioning, dependencies (required)
- **.skill/openai.json** - Framework-specific adapters (optional in Phase 1)
- **src/** - Implementation files (optional in Phase 1)
- **docs/** - Usage documentation (required)

### 2. Registry API Service (Node.js/Express)
**Core endpoints:**
```
GET    /api/v1/skills              # List all skills
GET    /api/v1/skills/:name        # Get skill metadata
GET    /api/v1/skills/:name/:v     # Get specific version
POST   /api/v1/skills              # Publish new skill
GET    /api/v1/search?q=...        # Search skills
GET    /api/v1/tags                # List tags
DELETE /api/v1/skills/:name/:v     # Unpublish version
GET    /api/v1/health              # Health check
```

**Database:** PostgreSQL with tables for:
- skills (name, description, author, license, etc.)
- versions (version, published_at, checksum, etc.)
- tags (for discovery)
- users (publisher authentication)

### 3. CLI Tool (Node.js)
**Commands:**
```bash
npm install -g @skills/cli

# Discovery
skills search "data analysis"
skills list                    # List installed skills
skills info data-analysis-expert

# Installation/Management
skills install data-analysis-expert
skills install data-analysis-expert@1.2.3
skills uninstall data-analysis-expert
skills update data-analysis-expert
skills registry list           # List configured registries

# Publishing
skills publish                 # Publish current folder as skill
skills publish --registry private.example.com
```

### 4. Local Cache & Loader
**Cache location:** `~/.skills/` or project-specific `./.skills/`

**Cache structure:**
```
~/.skills/
├── config.json              # Registry config, auth tokens
├── index.json               # Installed skills metadata
├── data-analysis-expert/
│   ├── 1.2.3/
│   │   ├── SKILL.md
│   │   ├── skill.yaml
│   │   └── ...
│   └── 1.2.2/
└── .cache/
    └── registry-metadata.json
```

**Loader:**
- Auto-load skills on startup
- Handle versioning/conflicts
- Non-blocking failure (warn if skill fails to load)

### 5. Security
- **Package signing:** GPG-based (publisher signs, registry verifies)
- **Checksum verification:** SHA256
- **Registry authentication:** JWT tokens for publishing
- **HTTPS only:** All API calls encrypted

### 6. Documentation
- **README.md** - Already done, needs minor updates
- **GETTING_STARTED.md** - Quick start guide
- **API_SPEC.md** - Full API documentation
- **CLI_REFERENCE.md** - Command reference
- **PUBLISHING_GUIDE.md** - How to publish a skill
- **EXAMPLES.md** - Example skill packages

---

## Implementation Phases (Sub-phases within Phase 1)

### Phase 1a: Design & Spec (Week 1)
**Deliverable: Complete specifications ready for coding**

- [ ] Finalize universal package format (SKILL.md + skill.yaml)
- [ ] Create detailed API specification (OpenAPI 3.0)
- [ ] Design database schema
- [ ] Define CLI command interface
- [ ] Create example skill packages
- [ ] Security model finalization

**Output:**
- API_SPEC.md with OpenAPI definitions
- Database schema diagram
- Example skill packages in repo
- CLI command documentation

### Phase 1b: Backend Registry (Week 2-3)
**Deliverable: Working registry API**

- [ ] Set up Node.js/Express project structure
- [ ] Implement database migrations (PostgreSQL)
- [ ] Implement all API endpoints
- [ ] Add authentication (JWT)
- [ ] Add package signing/verification
- [ ] Add rate limiting and validation
- [ ] Write comprehensive tests

**Output:**
- Deployable registry service
- All tests passing
- API documentation with examples

### Phase 1c: CLI Tool (Week 3-4)
**Deliverable: Working CLI for skill discovery and installation**

- [ ] Set up CLI project (TypeScript, Commander.js or similar)
- [ ] Implement discovery commands (search, list, info)
- [ ] Implement installation commands (install, uninstall, update)
- [ ] Implement cache management
- [ ] Implement skill loader
- [ ] Add configuration management
- [ ] Write comprehensive tests

**Output:**
- Published npm package (@skills/cli)
- All tests passing
- Installation guide

### Phase 1d: Integration & Polish (Week 4-5)
**Deliverable: Proven concept with Roundtable integration**

- [ ] Deploy registry to public hosting (AWS/Heroku/fly.io)
- [ ] Publish example skills to registry
- [ ] Integrate with Roundtable (test real usage)
- [ ] Fix bugs from real-world testing
- [ ] Write usage documentation
- [ ] Create marketing materials

**Output:**
- Live registry service
- Live CLI in npm
- Working Roundtable integration
- Complete documentation

---

## Technology Stack (Phase 1)

### Registry Service
- **Runtime:** Node.js 20+
- **Framework:** Express.js
- **Database:** PostgreSQL 14+
- **Authentication:** JWT (jsonwebtoken)
- **Signing:** node-pgp or similar
- **Hosting:** AWS/Heroku/fly.io (user's choice)

### CLI Tool
- **Language:** TypeScript
- **CLI Framework:** Commander.js or Yargs
- **HTTP Client:** axios or node-fetch
- **Package Manager:** npm
- **Publishing:** npm registry

### Local Cache
- **Storage:** File system (JSON + folders)
- **Config:** .skills/config.json
- **Loader:** Dynamic import/require

---

## Success Criteria

**Phase 1 is complete when:**

✅ Registry API deployed and accessible
✅ CLI tool published to npm and installable globally
✅ Can publish a skill to the registry
✅ Can search and discover skills
✅ Can install and load skills
✅ Roundtable can use published skills
✅ All components have tests (80%+ coverage)
✅ Documentation complete and examples working
✅ Security: package signing and verification working
✅ Zero critical security issues in code review

---

## Next Steps

1. **User Review:** (Right now)
   - Does this plan match your vision?
   - Any changes or additions?

2. **Specification Phase (1a):**
   - Finalize package format (validate SKILL.md approach)
   - Create API spec (OpenAPI 3.0)
   - Design database schema
   - Create example skills

3. **Development:**
   - Phase 1b: Registry backend
   - Phase 1c: CLI tool
   - Phase 1d: Integration & deployment

---

## Open Questions

1. **Registry Hosting:** Where should we host the proof-of-concept registry? (AWS, Heroku, fly.io, self-hosted?)
2. **CLI Package Name:** Should it be `@skills/cli`, `skill-manager`, or something else?
3. **Default Registry:** What should the default registry URL be? (e.g., registry.skills.dev?)
4. **Publishing Auth:** GitHub OAuth for publishing, or email/password?
5. **Monetization:** Will there be a free tier? Premium features?

---

## Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|------|-----------|--------|-----------|
| Scope creep during development | Medium | High | Strict focus on Phase 1 only, defer Phase 2 features |
| PostgreSQL setup complexity | Low | Medium | Use managed database (AWS RDS, Heroku Postgres) |
| Package signing adds complexity | Medium | Medium | Start simple (checksums), add GPG later if needed |
| Publishing workflow confusion | Medium | Medium | Clear documentation + examples |
| Security vulnerabilities in registry | Low | Critical | Security audit before launch, follow OWASP |

---

**Status:** Ready for design phase
**Created:** 2025-10-24
**Owner:** AlexZan