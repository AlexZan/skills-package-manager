# Phase 1 Scope: MVP Definition

## Overview

Phase 1 is the **Minimum Viable Product (MVP)** - the smallest set of features that proves the concept works and delivers core value.

**Goal:** Build a working skills registry that Roundtable can use to discover and load published skills.

**Timeline:** 6-8 weeks
**Cost:** $40/month
**Success Metric:** Users can publish npm skills and load them in Roundtable

---

## What's IN Phase 1

### 1. Registry API Service

**Core Endpoints (7 endpoints):**
```
GET    /api/v1/skills              List all skills (with pagination)
GET    /api/v1/skills/:name        Get skill metadata
GET    /api/v1/search?q=...        Search skills by name/description
GET    /api/v1/tags                List available tags
POST   /api/v1/skills              Publish new skill
GET    /api/v1/health              Health check
GET    /api/v1/version             API version
```

**Features:**
- ✅ RESTful API (OpenAPI 3.0 compatible)
- ✅ PostgreSQL database
- ✅ Basic pagination (limit/offset)
- ✅ Simple search (name + description + tags)
- ✅ Package signing (GPG-based verification)
- ✅ JWT authentication for publishing
- ✅ Error handling and validation

**Technology:**
- Node.js + Express.js
- PostgreSQL
- OpenAPI/Swagger documentation

---

### 2. CLI Tool

**Core Commands:**
```
skills search "analysis"           Search for skills
skills info data-analyst           Show skill details
skills install data-analyst        Install a skill locally
skills list                        List installed skills
skills uninstall data-analyst      Uninstall a skill
skills publish                     Publish current folder as skill
skills init                        Initialize new skill project
```

**Features:**
- ✅ Beautiful CLI with colors/formatting
- ✅ Interactive prompts for publishing
- ✅ Local skill cache (~/.skills/)
- ✅ Config management (.skillsrc)
- ✅ Automatic registry sync

**Technology:**
- TypeScript + Node.js
- Commander.js (CLI framework)
- Chalk (colors)
- Published to npm as @skills/cli

---

### 3. Skill Package Format

**SKILL.md (Anthropic-Compatible):**
```yaml
---
name: data-analyst
description: Analyzes financial data and creates reports
version: 1.0.0
author: yourname
license: MIT
---

## Instructions

You are an expert data analyst. Your job is to:
- Analyze financial statements
- Create visualizations
- Generate reports
...
```

**skill.yaml (Metadata Extensions):**
```yaml
name: data-analyst
version: 1.0.0
description: Analyzes financial data and creates reports
author: yourname
license: MIT
keywords:
  - data
  - analysis
  - finance
capabilities:
  - financial-analysis
  - data-visualization
  - reporting
frameworks:
  - roundtable: ">=1.0.0"
  - claude-api: ">=1.0.0"
homepage: https://github.com/yourname/data-analyst
repository: https://github.com/yourname/data-analyst
npm-package: "@skills/data-analyst"
checksum: sha256:abc123...
```

**Directory Structure:**
```
data-analyst-skill/
├── SKILL.md              # Claude-compatible instructions
├── skill.yaml            # Extended metadata
├── src/
│   ├── index.ts          # Implementation (optional)
│   └── types.ts          # TypeScript types (optional)
├── docs/
│   ├── README.md
│   └── EXAMPLES.md
├── tests/
│   └── skill.test.ts     # Basic tests
├── .skillrc              # Skill config (optional)
└── LICENSE
```

---

### 4. Local Cache & Skill Loading

**Cache Location:** `~/.skills/`

**Structure:**
```
~/.skills/
├── config.json           # Global config (registries, auth tokens)
├── index.json            # Installed skills metadata
├── data-analyst/
│   └── 1.0.0/
│       ├── SKILL.md
│       ├── skill.yaml
│       ├── src/
│       └── package.json
└── .cache/
    └── registry-metadata.json
```

**Loader Capabilities:**
- ✅ Auto-discover installed skills
- ✅ Load skill metadata
- ✅ Non-blocking failures (warn if skill fails to load)
- ✅ Version management

---

### 5. Security (Minimal Viable)

**Package Signing:**
- ✅ SHA256 checksum verification
- ✅ GPG signing (optional, for future)
- ✅ Basic integrity checks

**Authentication:**
- ✅ JWT tokens for publishing
- ✅ GitHub OAuth (optional Phase 2)
- ✅ Email/password registration

**Validation:**
- ✅ Schema validation (SKILL.md + skill.yaml)
- ✅ Malicious code detection (basic)
- ✅ Dependency validation

---

### 6. Documentation (For Phase 1)

**Provided:**
- ✅ API documentation (OpenAPI/Swagger)
- ✅ Quick start guide (5 min)
- ✅ Example skills (2-3 complete examples)
- ✅ CLI reference
- ✅ Publishing guide

---

### 7. Roundtable Integration

**What Works:**
- ✅ Roundtable can query the registry
- ✅ Roundtable can install published skills
- ✅ Roundtable can load skills at startup
- ✅ Roundtable agents can use loaded skills

**Not Required:**
- ❌ UI integration (CLI only in Phase 1)
- ❌ Dynamic skill loading at runtime (static at startup)

---

## What's OUT of Phase 1

### Explicitly Deferred to Later Phases

**Phase 2 Features:**
- ❌ PyPI integration (Python skills)
- ❌ Ruby Gems integration
- ❌ Marketplace web UI
- ❌ Ratings and reviews
- ❌ Advanced search (ML-based)
- ❌ GitHub OAuth integration
- ❌ Email notifications
- ❌ Analytics/dashboards
- ❌ Private registries
- ❌ RBAC (Role-Based Access Control)

**Phase 3+ Features:**
- ❌ Enterprise SLA support
- ❌ Commercial licenses
- ❌ Paid tiers
- ❌ Advanced features (composition, bundling)

**Why Deferred?**
- Not critical for MVP
- Can be added once core product proven
- Clearer requirements after Phase 1 feedback

---

## Success Criteria: Phase 1 is Done When...

### ✅ Technical Success

1. **Registry API**
   - [ ] 7 core endpoints implemented and tested
   - [ ] OpenAPI 3.0 spec complete
   - [ ] PostgreSQL schema designed and migrated
   - [ ] Authentication working (JWT)
   - [ ] Package signing/verification working
   - [ ] All tests passing (80%+ coverage)
   - [ ] Deployed to Fly.io and live

2. **CLI Tool**
   - [ ] All commands working
   - [ ] Published to npm (@skills/cli)
   - [ ] Installable globally (`npm install -g @skills/cli`)
   - [ ] All tests passing
   - [ ] --help and documentation complete

3. **Package Format**
   - [ ] SKILL.md spec complete (Anthropic-compatible)
   - [ ] skill.yaml spec complete (extended metadata)
   - [ ] Examples provided (2-3 complete skills)
   - [ ] Validation schema created
   - [ ] Documentation clear

4. **Integration**
   - [ ] Roundtable can discover skills from registry
   - [ ] Roundtable can install skills
   - [ ] Roundtable can load skills at startup
   - [ ] Roundtable agents can use loaded skills
   - [ ] Integration documented

### ✅ Quality Success

5. **Testing**
   - [ ] Unit tests (80%+ coverage)
   - [ ] Integration tests (basic flows)
   - [ ] End-to-end test (publish → search → install → use)
   - [ ] Security tests (auth, signing, validation)

6. **Documentation**
   - [ ] README with quick start
   - [ ] API documentation (OpenAPI)
   - [ ] CLI reference complete
   - [ ] Publishing guide with examples
   - [ ] Troubleshooting guide

7. **Security**
   - [ ] Zero critical vulnerabilities (npm audit clean)
   - [ ] Input validation on all endpoints
   - [ ] Authentication required for publishing
   - [ ] Package signatures verified
   - [ ] Secrets not in logs/errors

### ✅ Business Success

8. **Community Proof**
   - [ ] 5+ example skills published
   - [ ] Community member publishes a skill
   - [ ] Zero security incidents
   - [ ] Positive user feedback

---

## What We're NOT Doing (Out of Scope)

**Explicitly NOT in Phase 1:**
- ❌ Private registries (Phase 4)
- ❌ Marketplace ratings (Phase 3)
- ❌ Multi-language support beyond Node.js (Phase 2)
- ❌ Advanced dependency resolution (Phase 2)
- ❌ RBAC or permission management (Phase 4)
- ❌ Commercial/paid tiers (Phase 3)
- ❌ Web dashboard (Phase 3)
- ❌ Email notifications (Phase 3)
- ❌ Slack integration (Phase 3)
- ❌ GitHub webhooks (Phase 2)

---

## Definition of Done

**Phase 1 is complete when:**

1. ✅ Registry API deployed and stable
2. ✅ CLI published to npm and installable
3. ✅ Complete documentation
4. ✅ Roundtable integration working
5. ✅ 5+ published example skills
6. ✅ Zero critical security issues
7. ✅ 80%+ test coverage
8. ✅ Community can publish and discover skills

---

## Non-Goals

**We are NOT trying to:**
- ❌ Replace npm, pip, or Helm
- ❌ Build a for-profit business in Phase 1
- ❌ Support every programming language
- ❌ Compete with vendor registries
- ❌ Solve problems that don't exist yet

**We ARE trying to:**
- ✅ Prove the concept works
- ✅ Build a working MVP
- ✅ Validate market demand
- ✅ Create clear path to future phases

---

## Technical Constraints

### What We're Optimizing For

1. **Speed** - 6-8 weeks to launch
2. **Simplicity** - Minimal features, maximum clarity
3. **Clarity** - Code and docs crystal clear
4. **Testing** - 80%+ coverage (high quality)
5. **Security** - No critical vulnerabilities

### What We're NOT Optimizing For (Yet)

- ❌ Scalability to 1M requests/day (Phase 3)
- ❌ Complex search algorithms (Phase 3)
- ❌ Multi-region redundancy (Phase 4)
- ❌ Advanced caching (Phase 2)

---

## Risk Mitigation

### If We Can't Hit 6-8 Week Timeline

**Deferred to Phase 1.5:**
- Advanced error messages
- Comprehensive logging
- Performance optimization
- CLI color themes

**Rationale:** Core functionality ships, polish follows

### If Registry API Gets Complex

**Deferred to Phase 2:**
- Advanced query options
- Sorting/filtering
- Aggregations
- Analytics

**Rationale:** Start with simple search, add later

### If Package Format Is Unclear

**Deferred to Phase 1.5:**
- Framework adapters (.skill/openai.json)
- Optional fields
- Advanced features

**Rationale:** SKILL.md + skill.yaml sufficient for MVP

---

## Delivery Structure

### Week 1-2: Design & Setup
- [ ] API spec (OpenAPI 3.0)
- [ ] Database schema
- [ ] Project scaffolding
- [ ] Development environment

### Week 3-4: API Backend
- [ ] Core endpoints
- [ ] Database layer
- [ ] Authentication
- [ ] Tests

### Week 5-6: CLI Tool
- [ ] CLI commands
- [ ] Local cache
- [ ] Config management
- [ ] Tests

### Week 7-8: Integration & Polish
- [ ] Roundtable integration
- [ ] Documentation
- [ ] Deployment
- [ ] Bug fixes

### Week 8: Launch
- [ ] Deploy to production
- [ ] Publish CLI to npm
- [ ] Announce to community

---

## Acceptance Criteria: "It Works" Means...

**For an end user:**
1. Install CLI: `npm install -g @skills/cli`
2. Search: `skills search data`
3. See results with descriptions
4. Install: `skills install data-analyst`
5. Check: `skills list` (shows installed)
6. Use in Roundtable: Skill loads and works

**For a developer:**
1. Create skill folder with SKILL.md + skill.yaml
2. Run: `skills init` (creates project structure)
3. Publish: `skills publish` (uploads to registry)
4. Others can discover and install it

**For operations:**
1. Deploy to Fly.io: `flyctl deploy`
2. Monitor logs: `flyctl logs`
3. Scale if needed: `flyctl scale count 3`
4. Database migrations automatic

---

## Known Unknowns (To Resolve)

1. **Package format nuances** - How strict should validation be?
2. **API rate limiting** - Do we need it in Phase 1?
3. **Search algorithm** - Simple keyword match or more sophisticated?
4. **Error messages** - How verbose should they be?
5. **CLI interactivity** - Fully interactive or simple prompts?

**How to resolve:** Start simple, iterate based on feedback

---

## Rollout Plan

### Phase 1a: Private Alpha
- Deploy to staging
- Test with Roundtable team
- Fix critical bugs

### Phase 1b: Public Beta
- Deploy to production
- Announce to community
- Gather feedback
- Fix issues

### Phase 1c: General Availability
- Declare stable
- Full documentation
- Begin Phase 2 planning

---

## Dependencies

**External:**
- Node.js 18+
- PostgreSQL 14+
- npm registry (existing)
- Fly.io account

**Internal:**
- Roundtable codebase (for integration)
- CLAUDE.md conventions (for development)

**No new infrastructure needed** - all existing.

---

## Success Looks Like

By end of Phase 1:
- ✅ Users can publish skills to the registry
- ✅ Users can search for skills
- ✅ Users can install skills locally
- ✅ Roundtable loads and uses installed skills
- ✅ Community has published 5+ skills
- ✅ Zero security incidents
- ✅ Positive user feedback
- ✅ Clear path to Phase 2

---

**Status:** Scope Finalized
**Next:** Create detailed API specification
**Date:** 2025-10-24
