# Skills Package Manager

An open, vendor-agnostic package manager for AI agent skills. Works with Claude Skills, OpenAI tools, Google AI, and future frameworks - enabling applications to search, discover, and download skills on-demand.

## Vision

Create the "npm for AI agent skills" - an open standard and registry where:
- Skills are **format-agnostic** (Claude, OpenAI, Google, etc. all work)
- Developers can **search and discover** skills across the entire ecosystem
- Skills are **portable** across different LLM providers
- Skills have **proper versioning** and **dependency resolution**
- Anyone can **publish and share** their skills
- Organizations can run **private registries**

**Why this matters:** Anthropic released Claude Skills but no distribution mechanism. OpenAI will inevitably release their own format. The opportunity is to build the open standard NOW that becomes the lingua franca for AI agent skills—similar to how npm became the standard for JavaScript.

## Key Benefits

- **Reduced Bundle Size** - Apps only include core runtime, download skills as needed
- **Community-Driven** - Community can contribute and share skills
- **Dynamic Discovery** - Apps can discover new skills without rebuilding
- **Flexible Versioning** - Different apps can use different skill versions
- **Decoupled Development** - Skills can evolve independently from applications

## Use Cases

### Mobile App
```
User launches mobile app → App detects low storage
App downloads only essential skills for immediate tasks
User later needs specialized skill → App downloads on-demand
```

### Web Application
```
User selects "Data Science" mode
App discovers available data science skills
User selects specific skills to install
App downloads and integrates selected skills
```

### Enterprise System
```
Organization maintains private skill registry
Apps authenticate and download approved skills
Skills are versioned and managed centrally
```

## Status

🎯 **Research Complete, Planning Phase** - Market analysis and architecture design done

**Market Research Findings:**
- Anthropic Skills exist (Apache 2.0) but have no public distribution/registry
- A2A (Agent2Agent) protocol exists (Google, Apache 2.0) for agent collaboration
- MCP (Model Context Protocol) exists (Anthropic) for tool/model integration
- No vendor-neutral skills registry exists yet
- OpenAI/Google haven't released their skill formats publicly yet
- **Window of opportunity is NOW** - build before vendors standardize individually

**Strategic Approach:**
1. Start with Claude Skills compatibility (Phase 1)
2. Add OpenAI/Google support as they release formats (Phase 2)
3. Contribute to/lead standards discussions (A2A, MCP, or new standard)
4. Use Roundtable as flagship proof-of-concept

Implementation begins after Roundtable MVP completion. See main [Roundtable CLI](https://github.com/AlexZan/roundtable-cli) for current work.

## Related Documentation

- [VISION.md](./VISION.md) - Detailed project vision and goals
- [ARCHITECTURE.md](./ARCHITECTURE.md) - System design and technical approach

## Links

- **Main Project:** [Roundtable CLI](https://github.com/AlexZan/roundtable-cli)
- **Organization:** [AlexZan](https://github.com/AlexZan)
