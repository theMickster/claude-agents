# Claude Code Marketplace — Your Context Guide 🎯

Hey Claude! This is your insider's guide to the **Claude Code Marketplace** project.
Read this to understand what you're working on and how to help theMickster build epic shit.

---

## 🎬 What Is This Project?

This is a **production-grade marketplace** for Claude Code SDK components. Think of it as a curated collection of battle-tested tools that enterprise software engineering teams actually use in production.

### Four Pillars:

1. **🤖 Agents** — Specialized sub-agents for complex, multi-step workflows
2. **✍️ Prompts** — Reusable prompt templates for consistent outputs
3. **⚡ Commands** — Custom slash commands for rapid task execution
4. **🔌 Plugins** — MCP server integrations for external tools

---

## 🎯 Mission & Philosophy

### The Mission

Build a marketplace where every component is:

- ✅ **Production-ready** — Not experimental, not proof-of-concept
- ✅ **Enterprise-grade** — Built for teams shipping real software
- ✅ **Battle-tested** — Used in actual production environments
- ✅ **Well-documented** — Clear usage examples and setup guides

### The Vibe

- **Professional but fun** — Emojis for visual flow, but serious about quality
- **No BS** — Skip the fluff, focus on value
- **Smooth as warm butter** — Easy to read, easy to navigate, easy to use
- **Developer-first** — Built by engineers, for engineers

### What This Is NOT

- ❌ A toy project or demo collection
- ❌ Experimental code that might break
- ❌ Overly academic or theoretical
- ❌ Bloated with unnecessary features

---

## 🏗️ Repository Structure

```
claude-agents/
├── .claude/
│   ├── CLAUDE.md           # ← You are here
│   ├── prompts/            # Reusable prompt templates
│   └── commands/           # Custom slash commands
├── .claude-plugin/
│   └── marketplace.json    # Plugin and agent registry
├── plugins/
│   ├── engineering-orchestrator/
│   │   ├── engineering-orchestrator.md
│   │   └── README.md
│   └── database-architect/
│       ├── agents/
│       │   ├── database-advisor.md
│       │   ├── sql-server-architect.md
│       │   └── cosmosdb-architect.md
│       ├── reference/      # On-demand knowledge base
│       │   ├── database-selection-guide.md
│       │   ├── advisor-decision-examples.md
│       │   ├── sql-server-best-practices.md
│       │   ├── output-templates.md
│       │   └── qa-checklist.md
│       └── README.md
└── README.md              # Public-facing marketplace docs
```

### Where Things Go

| Component Type | Location | File Format | Notes |
| -------------- | -------- | ----------- | ----- |
| **Plugins** | `/plugins/{plugin-name}/` | Directory with README.md | Plugin container |
| **Agents** | `/plugins/{plugin-name}/agents/*.md` | Markdown files with frontmatter | Agent definitions |
| **Reference** | `/plugins/{plugin-name}/reference/*.md` | Markdown files | On-demand knowledge |
| **Prompts** | `/.claude/prompts/*.md` | Markdown files | Follow Claude Code format |
| **Commands** | `/.claude/commands/*.md` | Markdown files | Slash command definitions |

---

## 🎓 Your Role as Claude Code

When working in this repository, you should:

### 1. **Maintain Production Standards**

Every component you help create must be production-grade:

- Clean, maintainable code
- Comprehensive error handling
- Clear documentation with examples
- Security best practices (no hardcoded secrets!)
- Performance optimization for scale

### 2. **Keep the Energy High**

Match the tone of the README:

- Professional but approachable
- Use emojis for visual hierarchy (but don't overdo it)
- Write clear, punchy descriptions
- Focus on practical value over theory

### 3. **Think Enterprise-Scale**

Consider:

- Large codebases (millions of lines)
- Team collaboration (multiple engineers)
- Security and compliance requirements
- Performance at scale
- Integration with existing tooling

### 4. **Document Everything**

For each component you help create:

- **What it does** — Clear, one-sentence summary
- **When to use it** — Specific use cases
- **How to use it** — Step-by-step examples
- **Prerequisites** — Dependencies and setup
- **Limitations** — What it can't do

---

## 🛠️ Component Creation Guidelines

### For Plugins (`/plugins/`)

Plugins are containers for related agents and functionality.

**Structure:**

```
plugins/
└── {plugin-name}/
    ├── README.md           # Plugin overview
    ├── agents/             # Agent definitions
    │   ├── {agent-1}.md
    │   └── {agent-2}.md
    └── reference/          # Reference materials (optional)
        ├── {guide-1}.md
        └── {guide-2}.md
```

**Quality Bar:**

- Clear plugin purpose and scope
- Well-organized agent hierarchy
- Reference materials for efficient context usage
- Comprehensive documentation

**Current Plugins:**

- `engineering-orchestrator` — Meta-agent for coordinating complex workflows
- `database-architect` — Database technology decisions and implementation

---

### For Agents (`/plugins/{plugin-name}/agents/`)

Agents are autonomous task executors with specialized expertise.

**File Format:**

```markdown
---
name: agent-name
description: Brief description of what this agent does
tools: Glob, Grep, Read, ...
model: opus | sonnet | haiku
color: blue
---

You are a specialized agent...

## **INSTRUCTIONS FOR USE**

**CRITICAL**: Follow these patterns:

### 1. Progressive Disclosure
- Ask strategic questions before jumping to solutions
- Uncover requirements through targeted questioning

### 2. Chain-of-Thought Reasoning
- Use `<thinking>` tags to show your reasoning
- Analyze trade-offs transparently

### 3. Reference Materials (When Needed)
- Read reference files on-demand for deep knowledge
- Keep agent prompt lean, load context only when needed

...
```

**Quality Bar:**

- **Progressive disclosure** — Ask before assuming
- **Chain-of-thought** — Think transparently with `<thinking>` tags
- **Reference materials** — Load deep knowledge on-demand
- **Single responsibility** — One agent, one job
- **Graceful error handling** — Fail gracefully with helpful messages

**Agent Patterns:**

**Meta-agents** (orchestration):
- Discover available agents from `marketplace.json`
- Coordinate specialized agents for complex tasks
- Use progressive disclosure to gather requirements
- Route to appropriate implementation agents

**Implementation agents** (specialized):
- Deep expertise in specific domain
- Research-first approach (use MCP tools when applicable)
- Chain-of-thought reasoning
- Reference materials for efficient context

**Example Meta-Agent:**
- `engineering-orchestrator` — Coordinates database, frontend, backend agents

**Example Implementation Agents:**
- `sql-server-architect` — SQL Server schema and optimization
- `cosmosdb-architect` — Cosmos DB design and performance

---

### For Reference Materials (`/plugins/{plugin-name}/reference/`)

Reference files contain deep knowledge loaded on-demand by agents.

**Purpose:**
- Keep agent prompts lean (efficient context usage)
- Provide deep knowledge only when needed
- Avoid front-loading agents with encyclopedic content

**File Format:**

```markdown
# Reference Guide Title

Deep knowledge content here...

## Section 1
...

## Section 2
...
```

**When to create reference materials:**
- Database categories and selection criteria
- Technology comparison matrices
- Best practices and patterns
- Decision frameworks
- Example interactions

**Example reference files:**
- `database-selection-guide.md` — Database types, CAP theorem, workload patterns
- `advisor-decision-examples.md` — Complete interaction examples
- `sql-server-best-practices.md` — T-SQL optimization, indexing strategies

---

### For Prompts (`/.claude/prompts/`)

Prompts are reusable templates for consistent outputs.

**File Format:**

```markdown
---
description: Brief description of what this prompt does
---

# Prompt Title

Your prompt content here...
```

**Quality Bar:**

- Structured, consistent output format
- Context-aware and project-specific
- Include examples of expected output
- Version control for changes

**Example Prompt Ideas:**

- `pr-review.md` — Pull request review template
- `bug-analysis.md` — Root cause investigation
- `feature-spec.md` — Technical specification
- `api-design.md` — REST/GraphQL API design
- `refactor-plan.md` — Safe refactoring strategy

---

### For Commands (`/.claude/commands/`)

Commands are slash commands for rapid execution.

**File Format:**

```markdown
---
description: Brief description for /help menu
---

Your command implementation here...
```

**Quality Bar:**

- Fast execution (< 30s when possible)
- Clear, verb-based naming
- Confirmation for destructive operations
- Rich, helpful output messages

**Example Command Ideas:**

- `/ship` — Pre-deployment validation
- `/review-security` — Security scan
- `/optimize` — Performance profiling
- `/test-coverage` — Coverage analysis
- `/docs-update` — Documentation sync

---

## 💡 When the User Asks You To...

### "Add a new plugin"

1. Create directory in `/plugins/{plugin-name}/`
2. Create `README.md` with plugin overview
3. Create `agents/` subdirectory
4. Create `reference/` subdirectory (if needed)
5. Register in `.claude-plugin/marketplace.json`
6. Update main README with plugin description

### "Add a new agent"

1. Determine which plugin it belongs to
2. Create file in `/plugins/{plugin-name}/agents/{agent-name}.md`
3. Include frontmatter with name, description, tools, model, color
4. Implement progressive disclosure + chain-of-thought pattern
5. Create reference materials if deep knowledge needed
6. Register in `.claude-plugin/marketplace.json` under plugin
7. Update main README with agent description

### "Create reference material"

1. Create file in `/plugins/{plugin-name}/reference/{name}.md`
2. Structure for on-demand consumption by agents
3. Keep content focused and scannable
4. Link from agent instructions when to read it
5. DON'T duplicate content in agent prompt

### "Create a prompt template"

1. Create file in `/.claude/prompts/{name}.md`
2. Include frontmatter with description
3. Structure for consistent output
4. Add example usage
5. Update main README stats

### "Build a slash command"

1. Create file in `/.claude/commands/{name}.md`
2. Include frontmatter with description
3. Make it fast and focused
4. Add error handling
5. Update main README stats

### "Review or improve existing code"

- Apply production-grade standards
- Check for security issues
- Optimize for performance
- Improve documentation
- Maintain the established tone and style

---

## 🔥 Key Success Metrics

This marketplace succeeds when:

1. **Adoption** — Real teams use these components in production
2. **Quality** — Zero security vulnerabilities, high performance
3. **Clarity** — Anyone can understand and use components in < 5 minutes
4. **Maintainability** — Clean code that's easy to update and extend
5. **Community** — Contributors add high-quality components

---

## 🚨 Red Flags to Watch For

If you see any of these, flag them immediately:

- ❌ Hardcoded secrets or credentials
- ❌ Unclear or missing documentation
- ❌ Overly complex abstractions
- ❌ Poor error handling
- ❌ Security vulnerabilities
- ❌ Performance bottlenecks
- ❌ Breaking changes without version updates
- ❌ Agents with bloated prompts (move to reference materials!)
- ❌ Missing progressive disclosure or chain-of-thought patterns

---

## 🎯 Remember

This marketplace is about **shipping production code**. Every component should be something you'd trust in a mission-critical application. When in doubt:

1. **Prioritize clarity over cleverness**
2. **Optimize for real-world use cases**
3. **Document like your teammate depends on it** (they do!)
4. **Test like it's going to production** (it is!)
5. **Keep the energy high and the code clean** 🔥

---

## 🤝 Working with the User

The user is building this marketplace to help enterprise teams ship better software faster. They want:

- **Production-grade quality** — No shortcuts
- **Clear documentation** — No assumptions
- **Smooth developer experience** — No friction
- **Fun but professional tone** — No boring corporate speak

When helping:

- Ask clarifying questions about requirements
- Suggest best practices from your knowledge
- Point out potential issues early
- Maintain the established style and energy
- Keep components focused and maintainable
- Use reference materials pattern for efficient context usage

---

**Ready to build something epic?** Let's make this marketplace the go-to resource for Claude Code SDK components. 🚀
