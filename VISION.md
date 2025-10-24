# Skills Package Manager - Vision Document

## Problem Statement

Currently, AI agent skills across the ecosystem are:
- **Fragmented** - Each framework has its own skill format (Anthropic, OpenAI, Google, etc.)
- **Not Discoverable** - No central registry to find and share skills across models
- **Not Portable** - Skills lock you into a specific LLM provider
- **Hard to Maintain** - No standardized versioning or dependency management
- **Siloed** - Skills can't be reused across projects or organizations
- **No Distribution Layer** - Anthropic has skills but no way to discover/share them

## Solution

An open, vendor-agnostic skills package manager:
- **Format-Agnostic** - Works with Claude skills, OpenAI tools, Google skills, etc.
- **Discoverable** - Central registry to search, discover, and share skills
- **Portable** - Skills can be used across different LLM providers
- **Versioned** - Proper versioning, dependencies, and conflict resolution
- **Open Standard** - Community-driven, not controlled by any single vendor
- **Community-Driven** - Anyone can publish and discover skills
- **Secure** - Package signing, verification, and optional sandboxing

## Why Now?

Anthropic released Claude Skills (Apache 2.0 open source) but **no distribution mechanism**. OpenAI will inevitably release their own format. The opportunity is to create an open standard NOW that can become the lingua franca for AI agent skills across all frameworks—similar to how npm became the standard for JavaScript packages.

**Our competitive advantage:**
- Build it first (before OpenAI, Google, Meta get organized)
- Make it truly vendor-agnostic (not owned by any single LLM provider)
- Lead the standard as it emerges (contribute to A2A, MCP, or create new standard)
- Roundtable is the flagship use case that proves it works

## Core Concepts

### Skill Package
```yaml
name: data-analysis-expert
version: 1.0.0
description: Expert system for data analysis and visualization
tags: [data-science, analytics, visualization]
author: roundtable-community
license: MIT
dependencies:
  - roundtable: ">=2.0.0"
registry: https://skills.roundtable.dev
```

### Registry
Centralized service that hosts skill metadata and packages:
- **Public Registry** - Community skills (like npm/PyPI)
- **Private Registries** - Enterprise/organization skills
- **Search API** - Discover skills by tags, keywords, capability

### Installation
```bash
roundtable skills install data-analysis-expert
roundtable skills install @org/internal-skill
roundtable skills search "machine learning"
```

### Runtime Integration
Applications discover and load skills:
```typescript
const skillManager = new SkillManager({
  registryUrl: 'https://skills.roundtable.dev',
  cacheDir: '.roundtable/skills'
});

const dataSkills = await skillManager.search('data-science');
const analysisExpert = await skillManager.install('data-analysis-expert@latest');
```

## Architecture Layers

### 1. Skill Package Format
- Standard skill packaging (zip/tar)
- Metadata file (YAML/JSON)
- Version management
- Dependency specification

### 2. Registry Service
- REST API for search/discovery
- Package storage and versioning
- Authentication and authorization
- Rate limiting and quotas

### 3. Client/Manager
- CLI commands for package management
- Runtime loader and cache
- Version resolution
- Security validation

### 4. Security Layer
- Package signing
- Registry authentication
- Sandbox/isolation for untrusted skills
- Audit logging

## Phases (Proposed)

### Phase 1: MVP - Roundtable + Claude Skills
- Universal skill package format (SKILL.md + skill.yaml)
- Claude Skills compatibility (native format, no conversion needed)
- Basic registry API (search, install, list)
- CLI commands for Roundtable
- Local cache management
- Package signing and verification
- **Goal:** Roundtable can install and use Claude skills from public registry

### Phase 2: Format Adapters & Expansion
- OpenAI tools format support (.skill/openai.json)
- Google Vertex format support (.skill/google.json)
- Framework detection and auto-format selection
- Dependency resolution across frameworks
- **Goal:** One skill package works across Claude, OpenAI, and Google ecosystems

### Phase 3: Distribution & Community
- Production registry service (scalable API)
- Public skill marketplace with search/discovery
- Community publishing workflow (verified publishers)
- Semantic versioning and dependency resolution
- CDN for package delivery
- **Goal:** Community can publish and discover skills worldwide

### Phase 4: Enterprise & Standards Leadership
- Private registries for organizations
- RBAC (Role-Based Access Control)
- Audit trails and compliance features
- Enterprise skill bundles
- Contribute to A2A or MCP standards
- **Goal:** Enterprise adoption + position as reference implementation for open standards

### Phase 5: Ecosystem Leadership
- Support for additional frameworks as they emerge
- Skill composition and bundling
- Marketplace with ratings and recommendations
- Advanced discovery (ML-based recommendations)

## Success Metrics

- **Adoption:** Number of published community skills
- **Reuse:** Skills used across multiple applications
- **Performance:** Time to discover and install skills
- **Community:** Active contributors and maintainers
- **Security:** Zero security incidents, verified packages

## Open Questions

- **Interop with Anthropic:** Will Anthropic support/endorse this approach? Should we contribute back to their skill system?
- **Standard Leadership:** Should we help lead A2A protocol adoption, or propose a new open standard for skills?
- **Framework Support Timeline:** Which frameworks should we support in Phase 2 (OpenAI, Google, others)?
- **Governance:** Community governance model? Code of conduct? Verified publishers?
- **Monetization:** Should premium skills be supported? Revenue sharing model?
- **Security:** How strict should verification be? Risk vs usability tradeoff?
- **Discoverability:** How do we ensure skills are findable without AI-generated spam?

## Related Concepts

- **npm** - JavaScript package manager (distribution/versioning model)
- **PyPI** - Python package index (community publishing)
- **Anthropic Skills** - Claude Skills format (Phase 1 compatibility)
- **MCP (Model Context Protocol)** - Anthropic's open standard for tool/model integration
- **A2A (Agent2Agent Protocol)** - Google's open standard for agent collaboration
- **Helm** - Kubernetes package manager (registry and discovery patterns)
