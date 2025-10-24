# Use Cases: Skills Registry in Action

## Overview

This document provides two detailed, real-world use cases that demonstrate how the Skills Registry works end-to-end, from the perspective of both a skill developer and a skill consumer.

Each use case walks through the complete workflow, showing API calls, CLI commands, and expected results.

---

## Use Case 1: Financial Analyst Publishes a Skill

### Scenario

**Person:** Sarah, a financial data analyst who wants to share her expertise as a reusable AI skill.

**Goal:** Publish a financial analysis skill to the registry so other developers can discover and use it in their projects.

**Context:** Sarah has built a financial analysis module and wants the AI community to leverage it. She'll publish it as an npm package and register it with the skills registry.

---

### Step 1: Sarah Creates the Skill Package

**Directory Structure:**
```
financial-analyst-skill/
├── SKILL.md                    # Claude-compatible instructions
├── skill.yaml                  # Extended metadata
├── src/
│   ├── index.ts               # Main implementation
│   ├── financial-analysis.ts  # Financial analysis logic
│   └── types.ts               # TypeScript types
├── docs/
│   ├── README.md
│   └── EXAMPLES.md
├── tests/
│   └── financial-analyst.test.ts
├── package.json               # npm metadata
└── LICENSE
```

**SKILL.md (Claude Instructions):**
```markdown
---
name: financial-analyst
description: Expert financial analyst for analyzing statements and projections
version: 1.0.0
author: Sarah Chen
---

## Instructions

You are an expert financial analyst with 15 years of experience analyzing company financials.

### Your Role
- Analyze financial statements (P&L, balance sheet, cash flow)
- Identify trends and anomalies
- Create financial projections
- Evaluate financial health
- Compare against industry benchmarks

### When to Use This Skill
- User asks about financial analysis
- User asks about interpreting financial statements
- User needs financial projections
- User asks about company financial health

### Format Your Responses
1. **Summary**: Brief executive summary
2. **Analysis**: Detailed findings
3. **Recommendations**: Actionable insights
4. **Caveats**: Important limitations

### Important Notes
- Always cite specific financial metrics
- Include confidence levels for projections
- Reference industry benchmarks
- Note any data limitations
```

**skill.yaml (Extended Metadata):**
```yaml
name: financial-analyst
version: 1.0.0
description: Expert financial analyst for analyzing statements and projections
author:
  name: Sarah Chen
  email: sarah@example.com
  url: https://example.com
license: MIT
keywords:
  - finance
  - analysis
  - accounting
  - business
  - numbers
capabilities:
  - financial-statement-analysis
  - trend-analysis
  - financial-projections
  - industry-benchmarking
  - risk-assessment
frameworks:
  roundtable: ">=1.0.0"
  claude-api: ">=1.0.0"
homepage: https://github.com/sarahchen/financial-analyst-skill
repository: https://github.com/sarahchen/financial-analyst-skill
npmPackage: "@skills/financial-analyst"
```

---

### Step 2: Sarah Publishes to npm

**Action:** Sarah runs npm publish to create a public npm package.

```bash
$ cd financial-analyst-skill
$ npm version 1.0.0
$ npm publish
```

**Result:** Package published to npm registry as `@skills/financial-analyst@1.0.0`
- URL: https://www.npmjs.com/package/@skills/financial-analyst
- Accessible globally via: `npm install @skills/financial-analyst`

---

### Step 3: Sarah Registers with Skills Registry

**Action:** Sarah uses the CLI to register her npm package with the skills registry.

```bash
$ skills publish
? Name: financial-analyst
? Description: Expert financial analyst for analyzing statements and projections
? Keywords (comma-separated): finance, analysis, accounting
? Capabilities (comma-separated): financial-statement-analysis, trend-analysis, projections
? Frameworks: roundtable, claude-api
? npm package: @skills/financial-analyst
? npm version: 1.0.0

Registering skill with registry...
✓ Verified npm package exists
✓ Downloaded SKILL.md and skill.yaml
✓ Generated checksum (SHA256)
✓ Publishing to registry...

Success! ✓

Skill published to registry
├─ Name: financial-analyst
├─ Version: 1.0.0
├─ NPM Package: @skills/financial-analyst
├─ Registry URL: https://registry.skills.dev/api/v1/skills/financial-analyst
└─ Direct install: skills install financial-analyst
```

---

### Step 4: Registry Stores the Skill

**What Happens Behind the Scenes:**

The CLI makes an HTTP POST request to the registry:

```http
POST /api/v1/skills
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "name": "financial-analyst",
  "version": "1.0.0",
  "description": "Expert financial analyst for analyzing statements and projections",
  "author": {
    "name": "Sarah Chen",
    "email": "sarah@example.com"
  },
  "license": "MIT",
  "keywords": ["finance", "analysis", "accounting"],
  "capabilities": ["financial-statement-analysis", "trend-analysis", "projections"],
  "frameworks": {
    "roundtable": ">=1.0.0",
    "claude-api": ">=1.0.0"
  },
  "homepage": "https://github.com/sarahchen/financial-analyst-skill",
  "repository": "https://github.com/sarahchen/financial-analyst-skill",
  "npmPackage": "@skills/financial-analyst",
  "npmVersion": "1.0.0",
  "checksum": "sha256:a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6"
}
```

**Registry Response (201 Created):**
```json
{
  "success": true,
  "data": {
    "id": "uuid-skill-001",
    "name": "financial-analyst",
    "version": "1.0.0",
    "message": "Skill published successfully",
    "url": "https://registry.skills.dev/api/v1/skills/financial-analyst",
    "npmUrl": "https://www.npmjs.com/package/@skills/financial-analyst",
    "publishedAt": "2025-01-20T14:22:00Z"
  }
}
```

**Database Changes:**

Three tables are updated:

```sql
-- skills table
INSERT INTO skills (id, name, created_by, created_at) VALUES
  ('uuid-skill-001', 'financial-analyst', 'sarah_chen', '2025-01-20T14:22:00Z');

-- versions table
INSERT INTO versions (id, skill_id, version, description, author, keywords, capabilities,
  frameworks, npm_package, npm_version, checksum, published_at) VALUES
  ('uuid-version-001', 'uuid-skill-001', '1.0.0', 'Expert financial analyst...',
   'Sarah Chen', 'finance,analysis,accounting',
   'financial-statement-analysis,trend-analysis,projections',
   'roundtable,claude-api', '@skills/financial-analyst', '1.0.0',
   'sha256:a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6', '2025-01-20T14:22:00Z');

-- tags table
INSERT INTO tags (skill_id, tag) VALUES
  ('uuid-skill-001', 'finance'),
  ('uuid-skill-001', 'analysis'),
  ('uuid-skill-001', 'accounting');
```

---

### Step 5: Community Discovers the Skill

**Weeks Later:** Other developers discover Sarah's skill.

**Developer 1 (Marcus) searches the registry:**

```bash
$ skills search "finance"
```

**CLI makes GET request:**
```http
GET /api/v1/search?q=finance&limit=20
```

**Response:**
```json
{
  "success": true,
  "data": {
    "results": [
      {
        "id": "uuid-skill-001",
        "name": "financial-analyst",
        "version": "1.0.0",
        "description": "Expert financial analyst for analyzing statements and projections",
        "author": {
          "name": "Sarah Chen",
          "email": "sarah@example.com"
        },
        "keywords": ["finance", "analysis", "accounting", "business"],
        "capabilities": ["financial-statement-analysis", "trend-analysis", "projections"],
        "frameworks": ["roundtable", "claude-api"],
        "downloads": 12,
        "rating": 4.8,
        "matchScore": 0.98,
        "publishedAt": "2025-01-20T14:22:00Z"
      }
    ],
    "pagination": {
      "limit": 20,
      "offset": 0,
      "total": 1,
      "hasMore": false
    }
  }
}
```

---

### Step 6: Marcus Installs and Uses the Skill

**Marcus sees the skill in search results and wants to use it:**

```bash
$ skills install financial-analyst
```

**What the CLI Does:**

1. **Queries registry for skill details:**
```http
GET /api/v1/skills/financial-analyst
```

2. **Gets metadata response:**
```json
{
  "success": true,
  "data": {
    "name": "financial-analyst",
    "versions": [
      {
        "version": "1.0.0",
        "npmPackage": "@skills/financial-analyst",
        "npmVersion": "1.0.0",
        "checksum": "sha256:a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6",
        "publishedAt": "2025-01-20T14:22:00Z"
      }
    ]
  }
}
```

3. **Runs npm install to get the actual package:**
```bash
npm install @skills/financial-analyst
```

4. **Stores in local cache:**
```
~/.skills/
├── financial-analyst/
│   └── 1.0.0/
│       ├── SKILL.md
│       ├── skill.yaml
│       └── src/
└── index.json
```

5. **Updates index.json:**
```json
{
  "installedSkills": [
    {
      "name": "financial-analyst",
      "version": "1.0.0",
      "installedAt": "2025-01-25T10:30:00Z",
      "source": "https://registry.skills.dev",
      "checksum": "sha256:a1b2c3d4e5f6g7h8i9j0k1l2m3n4o5p6"
    }
  ]
}
```

---

### Step 7: Marcus Uses in Roundtable

**Marcus starts Roundtable, which automatically loads the skill:**

```typescript
// In Roundtable startup code
const skillManager = new SkillManager({
  cacheDir: '~/.skills'
});

// Load all installed skills
const skills = await skillManager.loadAll();
console.log(skills); // Includes financial-analyst
```

**When Marcus runs Roundtable with a financial question:**

```bash
$ npm run dev
> Roundtable CLI initialized
> Loaded skills: financial-analyst, data-analyst, ...
> Ready for debate

? Enter your question:
> Should I invest in Company XYZ? Here's their financial statement...
```

**Roundtable Workflow:**

1. User provides financial question
2. Roundtable loads financial-analyst skill (from cache)
3. Roundtable reads SKILL.md instructions
4. Roundtable invokes Claude with:
   - User's question
   - Loaded skill instructions
   - Financial-analyst context
5. Claude (with financial-analyst skill context) provides expert analysis

---

### Summary: Use Case 1 Flow

```
Sarah creates skill
    ↓
Sarah publishes to npm (@skills/financial-analyst)
    ↓
Sarah registers with skills registry (via CLI)
    ↓
Registry stores metadata + creates checksums
    ↓
Marcus searches registry ("finance")
    ↓
Marcus installs skill (via CLI)
    ↓
CLI downloads npm package + caches locally
    ↓
Marcus uses in Roundtable
    ↓
Roundtable loads skill, Claude uses skill instructions
    ↓
Expert financial analysis provided
```

---

---

## Use Case 2: Organization Sets Up Skill Discovery Workflow

### Scenario

**Organization:** TechFlow, a software development company

**People:**
- Alex (Engineering Lead)
- Team of 5 developers building AI-powered tools

**Goal:** Discover, vet, and use pre-built skills instead of building from scratch. Save development time and leverage community expertise.

**Context:** TechFlow is building a data analytics platform. Instead of building analysis skills from scratch, they want to:
1. Discover existing skills
2. Evaluate them (code quality, ratings, maintenance)
3. Install and integrate
4. Extend with custom skills

---

### Step 1: Alex Discovers Available Skills

**Action:** Alex uses the CLI to explore available skills in the registry.

```bash
$ skills search --capabilities="analysis,visualization" --frameworks="roundtable" --limit=50
```

**CLI Request:**
```http
GET /api/v1/search?capabilities=analysis,visualization&frameworks=roundtable&limit=50
```

**Response Shows 15 Skills:**
```
┌─ Analysis & Visualization Skills ────────────────────┐
│                                                       │
│ 1. financial-analyst (1.0.0)                        │
│    By: Sarah Chen                                    │
│    ✓ financial-statement-analysis                   │
│    ✓ trend-analysis                                 │
│    ✓ financial-projections                          │
│    ⭐ 4.8/5 (12 downloads)                          │
│                                                       │
│ 2. data-scientist (2.1.0)                           │
│    By: Kumar Patel                                   │
│    ✓ statistical-analysis                           │
│    ✓ data-visualization                             │
│    ✓ machine-learning-insights                      │
│    ⭐ 4.6/5 (450 downloads)                         │
│                                                       │
│ 3. business-analyst (1.5.2)                         │
│    By: Emma Watson                                   │
│    ✓ business-metrics-analysis                      │
│    ✓ trend-forecasting                              │
│    ✓ report-generation                              │
│    ⭐ 4.7/5 (203 downloads)                         │
│                                                       │
│ ... (12 more skills)                                │
│                                                       │
└────────────────────────────────────────────────────── ┘
```

---

### Step 2: Alex Evaluates Specific Skills

**Action:** Alex gets details on the most promising skills.

```bash
$ skills info data-scientist
```

**CLI Request:**
```http
GET /api/v1/skills/data-scientist
```

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid-skill-002",
    "name": "data-scientist",
    "versions": [
      {
        "version": "2.1.0",
        "description": "Data scientist expert for statistical analysis and ML insights",
        "author": {
          "name": "Kumar Patel",
          "email": "kumar@example.com"
        },
        "license": "Apache-2.0",
        "keywords": ["data-science", "statistics", "machine-learning"],
        "capabilities": [
          "statistical-analysis",
          "data-visualization",
          "machine-learning-insights",
          "hypothesis-testing"
        ],
        "frameworks": {
          "roundtable": ">=1.0.0",
          "claude-api": ">=1.0.0"
        },
        "dependencies": {
          "pandas": ">=1.0.0",
          "scipy": ">=1.5.0"
        },
        "homepage": "https://github.com/kumarpatel/data-scientist-skill",
        "repository": "https://github.com/kumarpatel/data-scientist-skill",
        "npmPackage": "@skills/data-scientist",
        "npmVersion": "2.1.0",
        "publishedAt": "2025-01-10T08:15:00Z",
        "downloads": 450,
        "rating": 4.6,
        "reviews": [
          {
            "author": "Jane Smith",
            "rating": 5,
            "text": "Excellent statistical analysis. Very accurate."
          },
          {
            "author": "Bob Johnson",
            "rating": 4,
            "text": "Great skill, but documentation could be better."
          }
        ]
      },
      {
        "version": "2.0.0",
        "description": "Previous version (deprecated)",
        "publishedAt": "2024-12-20T10:00:00Z",
        "downloads": 150
      }
    ],
    "totalDownloads": 600,
    "createdAt": "2024-12-01T00:00:00Z"
  }
}
```

**Alex's Analysis:**
- ✅ High rating (4.6/5)
- ✅ Active maintained (recent version)
- ✅ Good download count (450)
- ✅ Apache-2.0 license (company-friendly)
- ✅ Good reviews
- ✅ Frameworks match: roundtable, claude-api

---

### Step 3: Team Evaluates Shortlist

**Action:** Alex presents to the team. They decide to use 3 core skills.

**Decision:**
1. ✅ **data-scientist** (2.1.0) - For data analysis
2. ✅ **business-analyst** (1.5.2) - For business metrics
3. ✅ **financial-analyst** (1.0.0) - For financial insights

---

### Step 4: Team Installs Chosen Skills

**Action:** Team installs all three skills in one go.

```bash
$ skills install data-scientist business-analyst financial-analyst
```

**CLI executes sequentially:**

**For each skill:**
1. Query registry for metadata
2. Download npm package
3. Verify checksum
4. Cache locally
5. Update index.json

**Output:**
```
Installing skills...

⏳ Installing data-scientist (2.1.0)
  ✓ Downloaded from @skills/data-scientist@2.1.0
  ✓ Verified checksum
  ✓ Cached to ~/.skills/data-scientist/2.1.0/
  ✓ Added to installed skills

⏳ Installing business-analyst (1.5.2)
  ✓ Downloaded from @skills/business-analyst@1.5.2
  ✓ Verified checksum
  ✓ Cached to ~/.skills/business-analyst/1.5.2/
  ✓ Added to installed skills

⏳ Installing financial-analyst (1.0.0)
  ✓ Downloaded from @skills/financial-analyst@1.0.0
  ✓ Verified checksum
  ✓ Cached to ~/.skills/financial-analyst/1.0.0/
  ✓ Added to installed skills

✅ All skills installed successfully

Installed Skills:
├─ data-scientist (2.1.0) - Data scientist expert for analysis
├─ business-analyst (1.5.2) - Business analyst expert for metrics
└─ financial-analyst (1.0.0) - Financial analyst for statements

Next: Run Roundtable with these skills loaded!
```

---

### Step 5: Verify Installation

**Action:** Team checks installed skills.

```bash
$ skills list
```

**Output:**
```
Installed Skills (3 total):

1. data-scientist
   Version: 2.1.0
   Installed: 2025-01-25 10:30:00
   Source: @skills/data-scientist@2.1.0
   Capabilities: statistical-analysis, data-visualization, ml-insights

2. business-analyst
   Version: 1.5.2
   Installed: 2025-01-25 10:31:00
   Source: @skills/business-analyst@1.5.2
   Capabilities: business-metrics, trend-forecasting, reports

3. financial-analyst
   Version: 1.0.0
   Installed: 2025-01-25 10:32:00
   Source: @skills/financial-analyst@1.0.0
   Capabilities: financial-analysis, trend-analysis, projections

Cache Location: ~/.skills/ (Total: 23.4 MB)
```

---

### Step 6: Integrate with Application

**Action:** Team's application loads skills on startup.

**In TechFlow's application code:**

```typescript
import { SkillManager } from '@skills/sdk';

async function initializeSkills() {
  const skillManager = new SkillManager({
    cacheDir: '~/.skills',
    autoLoad: true
  });

  // Load all installed skills
  const skills = await skillManager.loadAll();
  console.log(`Loaded ${skills.length} skills:`);

  skills.forEach(skill => {
    console.log(`  ✓ ${skill.name} (${skill.version})`);
    console.log(`    Capabilities: ${skill.capabilities.join(', ')}`);
  });

  return skillManager;
}

// In main application startup
const skills = await initializeSkills();
// Roundtable/Claude now has access to all loaded skills
```

---

### Step 7: Use Skills in Application

**Scenario: User asks an analytical question**

```
User: "Analyze our Q4 sales data and compare to Q3"
```

**What Happens:**

1. **Question routed to Roundtable debate**
2. **Roundtable evaluates loaded skills:**
   - ✓ data-scientist: "statistical-analysis" + "data-visualization" match
   - ✓ business-analyst: "business-metrics" matches
   - ✗ financial-analyst: Not relevant (not financial question)

3. **Roundtable invokes panel with relevant skills:**
   - Data Scientist (using data-scientist skill context)
   - Business Analyst (using business-analyst skill context)

4. **Claude with data-scientist context:**
   ```
   [SKILL CONTEXT: data-scientist]
   You are a data scientist expert. Analyze this data:
   - Q4 sales: $2.5M
   - Q3 sales: $2.1M
   - Growth: 19%

   Provide statistical analysis with confidence intervals and visualizations.
   ```

5. **Claude with business-analyst context:**
   ```
   [SKILL CONTEXT: business-analyst]
   You are a business analyst. Evaluate business impact:
   - Revenue growth: +19% QoQ
   - Customer acquisition: +15%
   - Product mix shift: +22%

   Provide business metrics and trend analysis.
   ```

6. **Results synthesized and presented to user:**
   ```
   ┌─ Expert Panel Analysis ─────────────────┐
   │                                          │
   │ Data Scientist Perspective:             │
   │ - 19% QoQ growth (95% confidence)      │
   │ - Trend: Statistically significant      │
   │ - Variance analysis: Low risk           │
   │                                          │
   │ Business Analyst Perspective:           │
   │ - Strong business metrics               │
   │ - Key drivers: Product mix, CAC        │
   │ - Recommendations: Scale in Q1         │
   │                                          │
   └────────────────────────────────────────┘
   ```

---

### Step 8: Team Extends with Custom Skill

**Later: Team wants a skill specific to their domain (SaaS metrics)**

**Action:** Team creates custom skill.

```bash
$ skills init saas-metrics
? Skill name: saas-metrics
? Description: Expert on SaaS key metrics (MRR, CAC, LTV, churn)
? Capabilities: saas-metrics, unit-economics, retention-analysis

Created new skill project: saas-metrics/
├── SKILL.md (edit instructions)
├── skill.yaml (metadata)
├── src/ (add implementation)
├── docs/ (add documentation)
└── tests/ (add tests)
```

**Team adds custom SKILL.md:**
```markdown
---
name: saas-metrics
description: Expert on SaaS key metrics and unit economics
version: 1.0.0
author: TechFlow
---

## Instructions

You are an expert in SaaS business metrics...
[Custom instructions for their domain]
```

**Team publishes internally to registry:**
```bash
$ skills publish --private
? Organization: techflow
? This is a private skill (only your org can use)

✓ Skill published to private registry
✓ Available at: https://registry.skills.dev/organizations/techflow/saas-metrics
```

---

### Step 9: Team Uses Both Community and Custom Skills

**Application now loads from both:**

```
~/.skills/
├── data-scientist/           (from community registry)
├── business-analyst/         (from community registry)
├── financial-analyst/        (from community registry)
└── saas-metrics/             (from private registry)
```

**When starting Roundtable:**
```bash
$ npm run dev

Loading skills...
✓ data-scientist (community)
✓ business-analyst (community)
✓ financial-analyst (community)
✓ saas-metrics (private organization)

Panel ready with 4 expert perspectives!
```

---

### Summary: Use Case 2 Flow

```
Alex searches for analysis skills
    ↓
Alex evaluates top candidates (ratings, reviews, downloads)
    ↓
Team selects 3 skills
    ↓
Team installs all 3 skills (1 command)
    ↓
CLI downloads, verifies, caches all 3
    ↓
Application loads skills on startup
    ↓
User asks question (Q4 sales analysis)
    ↓
Roundtable invokes relevant skill contexts
    ↓
Expert panel analyzes using loaded skills
    ↓
Results synthesized and presented
    ↓
Later: Team creates custom skill (saas-metrics)
    ↓
Application uses 4 skills (3 community + 1 private)
```

---

## Benefits Demonstrated

### Use Case 1 (Sarah - Publisher)

**Sarah's Benefits:**
- ✅ Share expertise with community
- ✅ Get feedback and ratings
- ✅ Build reputation
- ✅ Simple publish workflow (1 command)
- ✅ Automatic version management
- ✅ Leverage npm for distribution

---

### Use Case 2 (TechFlow - Consumer)

**TechFlow's Benefits:**
- ✅ Discover existing solutions (don't rebuild)
- ✅ Evaluate before using (ratings, reviews, downloads)
- ✅ Quick integration (1 install command)
- ✅ Extend with custom skills
- ✅ Access to expert domain knowledge
- ✅ Saved development time

---

## Key Interactions Demonstrated

### API Calls Shown:
1. ✅ `POST /api/v1/skills` - Publish skill
2. ✅ `GET /api/v1/search` - Search with filters
3. ✅ `GET /api/v1/skills/:name` - Get details
4. ✅ Registry response with metadata

### CLI Commands Shown:
1. ✅ `skills search` - Discover skills
2. ✅ `skills info` - Get details
3. ✅ `skills install` - Install skills
4. ✅ `skills list` - List installed
5. ✅ `skills publish` - Publish skill
6. ✅ `skills init` - Create new skill

### Database Operations Shown:
1. ✅ Insert into skills table
2. ✅ Insert into versions table
3. ✅ Insert into tags table

### Cache Operations Shown:
1. ✅ Store in ~/.skills/
2. ✅ Update index.json
3. ✅ Verify checksums

---

## Success Metrics Validation

**From Use Case 1 (Publisher):**
- ✅ Skill discovered by community (Marcus found it)
- ✅ Skill installed and used
- ✅ Download count increased
- ✅ Ratings accumulated

**From Use Case 2 (Consumer):**
- ✅ Discovered multiple skills
- ✅ Evaluated before using
- ✅ Installed successfully
- ✅ Integrated into application
- ✅ Used in real workflows
- ✅ Extended with custom skills

---

## Conclusion

These use cases demonstrate:

1. **For Skill Developers:**
   - Simple publish workflow
   - Community discovery
   - Reuse and adoption

2. **For Skill Consumers:**
   - Easy discovery and evaluation
   - Quick integration
   - Expert knowledge access
   - Extension capability

3. **For the Ecosystem:**
   - Skills shared and reused
   - Community contribution encouraged
   - Development time reduced
   - Expert knowledge distributed

Both use cases validate that the Skills Registry API, CLI, and workflow deliver real value to both publishers and consumers.

---

**Status:** Use Cases Complete and Validated Against Spec
**Date:** 2025-10-24
**Next:** Database Schema Specification
