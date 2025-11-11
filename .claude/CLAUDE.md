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
├── agents/                 # Sub-agents for specialized tasks
├── plugins/                # MCP server configurations
└── README.md              # Public-facing marketplace docs
```

### Where Things Go

| Component Type | Location                  | File Format              | Notes                            |
| -------------- | ------------------------- | ------------------------ | -------------------------------- |
| **Agents**     | `/agents/{agent-name}/`   | Directory with README.md | Each agent is self-contained     |
| **Prompts**    | `/.claude/prompts/*.md`   | Markdown files           | Follow Claude Code prompt format |
| **Commands**   | `/.claude/commands/*.md`  | Markdown files           | Slash command definitions        |
| **Plugins**    | `/plugins/{plugin-name}/` | Directory with config    | MCP server setup + docs          |

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

### For Agents (`/agents/`)

Agents are autonomous task executors with specialized expertise.

**Structure:**

```
agents/
└── {agent-name}/
    ├── README.md           # Documentation
    ├── prompt.md           # Agent system prompt
    ├── examples/           # Usage examples
    └── tests/              # Test cases (if applicable)
```

**Quality Bar:**

- Single, clear responsibility
- Well-defined inputs and outputs
- Graceful error handling
- Idempotent operations (safe to retry)
- Examples of real-world usage

**Example Agent Ideas:**

- `code-review-agent` — Comprehensive PR reviews
- `architecture-planner` — Technical design documents
- `test-generator` — Smart test suite creation
- `security-scanner` — Vulnerability detection
- `performance-optimizer` — Profiling and optimization

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

### For Plugins (`/plugins/`)

Plugins are MCP server integrations for external tools.

**Structure:**

```
plugins/
└── {plugin-name}/
    ├── README.md           # Setup and usage docs
    ├── config.json         # MCP server configuration
    └── examples/           # Integration examples
```

**Quality Bar:**

- Secure credential management
- Respect API rate limits
- Graceful offline handling
- Clear setup instructions

**Example Plugin Ideas:**

- CI/CD integration (GitHub Actions, CircleCI)
- Observability (DataDog, New Relic)
- Issue tracking (Jira, Linear)
- Code quality (SonarQube, CodeClimate)
- Security scanning (Snyk, Dependabot)

---

## 💡 When the User Asks You To...

### "Add a new agent"

1. Create directory in `/agents/{agent-name}/`
2. Write comprehensive README.md
3. Create the agent prompt file
4. Add usage examples
5. Update main README stats

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

### "Set up a plugin"

1. Create directory in `/plugins/{plugin-name}/`
2. Write setup documentation
3. Create MCP configuration
4. Add integration examples
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

---

**Ready to build something epic?** Let's make this marketplace the go-to resource for Claude Code SDK components. 🚀
