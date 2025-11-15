# 🚀 Claude Code Marketplace

**Production-grade agents, prompts, commands, and plugins for enterprise software engineering**

[![Claude Code SDK](https://img.shields.io/badge/Claude%20Code-SDK-blue)](https://github.com/anthropics/claude-code)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![Status](https://img.shields.io/badge/status-active-success)]()

> Transform your Claude Code workflow with battle-tested components built for real-world software engineering teams.

---

## 🎯 What's Inside

This marketplace is your one-stop shop for Claude Code SDK components that actually ship to production. No experiments, no demos—just tools that enterprise teams trust daily.

| Category | What You Get | When to Use |
|----------|--------------|-------------|
| 🤖 **Agents** | Autonomous task executors with specialized expertise | Complex multi-step workflows, domain-specific automation |
| ✍️ **Prompts** | Production-tested prompt templates | Consistent outputs, standardized workflows |
| ⚡ **Commands** | Custom slash commands for common tasks | Rapid execution, workflow shortcuts |
| 🔌 **Plugins** | Extended functionality via MCP servers | Integration with external tools and services |

---

## ⚡ Quick Start

### Installation

Get started with the Claude Code Collective in seconds:

```bash
/plugin install claude-code-collective
```

### Upgrade

Keep your components up to date:

```bash
/plugin upgrade claude-code-collective
```

That's it! All agents, prompts, commands, and plugins will be available in your Claude Code environment.

---

## 🤖 Agents

Specialized sub-agents that handle complex, multi-step tasks autonomously. Each agent is optimized for production workloads and enterprise-scale codebases.

### Engineering Orchestrator

**Meta-agent for coordinating complex software engineering workflows**

- **[engineering-orchestrator](plugins/engineering-orchestrator/)** — Orchestrate multi-domain tasks by coordinating specialized agents through progressive disclosure and deliberate reasoning. Use for authentication systems, performance optimization, migrations, or any task spanning backend, frontend, database, and DevOps.

### Database Architecture

**Strategic technology decisions and production-grade database design**

- **[database-advisor](plugins/database-architect/agents/database-advisor.md)** — Strategic advisor for critical database technology decisions. Evaluates SQL Server vs Cosmos DB vs PostgreSQL, analyzes trade-offs, and recommends platforms based on workload patterns, scale, and team capabilities.

- **[sql-server-architect](plugins/database-architect/agents/sql-server-architect.md)** — Design SQL Server schemas, optimize T-SQL queries, implement indexing strategies, and configure high availability solutions for enterprise databases.

- **[cosmosdb-architect](plugins/database-architect/agents/cosmosdb-architect.md)** — Design Azure Cosmos DB schemas, select partition keys, optimize RU consumption, implement change feeds, and configure multi-region deployments.

### Key Features

✅ **Progressive Disclosure** — Agents ask strategic questions to uncover requirements before jumping to solutions

✅ **Chain-of-Thought Reasoning** — Transparent decision-making using `<thinking>` tags

✅ **Reference Materials** — Deep knowledge loaded on-demand for efficient context usage

✅ **Agent Coordination** — Meta-agents discover and route to specialized implementation agents

---

## ✍️ Custom Prompts

Pre-built prompt templates that ensure consistency across your engineering team. Perfect for standardizing common tasks and maintaining quality bars.

### Available Prompts

_Coming soon — enterprise-ready prompts for:_
- **Pull Request Reviews** — Comprehensive code review with security scanning
- **Bug Analysis** — Root cause investigation with reproduction steps
- **Feature Planning** — Technical specs with architecture decisions
- **API Design** — REST/GraphQL design with versioning strategy
- **Performance Optimization** — Profiling-driven improvement recommendations

> **Add your own:** Place prompts in `/.claude/prompts` following the Claude Code prompt format.

---

## ⚡ Custom Commands

Lightning-fast slash commands for your most frequent workflows. One command, instant execution.

### Available Commands

_Coming soon — production commands for:_
- **/ship** — Pre-deployment checklist and validation
- **/review-security** — Security vulnerability scan with remediation steps
- **/optimize** — Performance profiling and bottleneck identification
- **/test-coverage** — Coverage analysis with gap recommendations
- **/docs-update** — Auto-update documentation based on code changes

> **Add your own:** Create commands in `/.claude/commands` directory with clear descriptions.

---

## 🔌 Plugins & MCP Servers

Extend Claude Code with powerful integrations. Connect to your existing tools, services, and workflows.

### Available Plugins

_Coming soon — enterprise integrations for:_
- **CI/CD Pipelines** — GitHub Actions, CircleCI, Jenkins integration
- **Observability** — DataDog, New Relic, Grafana monitoring
- **Issue Tracking** — Jira, Linear, GitHub Issues automation
- **Code Quality** — SonarQube, CodeClimate integration
- **Security Scanning** — Snyk, Dependabot, OWASP integration

> **Add your own:** Deploy MCP servers and register them with Claude Code configuration.

---

## 🏗️ Architecture

This marketplace follows Claude Code SDK best practices for maximum compatibility and maintainability.

```
claude-agents/
├── .claude/
│   ├── CLAUDE.md           # Project context for Claude Code
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
└── README.md
```

### Plugin Structure

Each plugin contains:
- **Agents** — Specialized sub-agents with focused expertise
- **Reference materials** — Deep knowledge loaded on-demand
- **Documentation** — Setup guides and usage examples

### Agent Patterns

**Meta-agents** (orchestration):
- Coordinate multiple specialized agents
- Use progressive disclosure to gather requirements
- Route to appropriate implementation agents

**Implementation agents** (specialized):
- Deep expertise in specific domains
- Research-first approach using Microsoft docs tools
- Chain-of-thought reasoning with reference materials

---

## 🎓 Best Practices

### For Agents
- ✅ Progressive disclosure — Ask before assuming
- ✅ Chain-of-thought reasoning — Think transparently
- ✅ Reference materials — Load knowledge on-demand
- ✅ Single responsibility — One agent, one job
- ✅ Error handling — Graceful degradation

### For Prompts
- ✅ Structured output — Consistent format for parsing
- ✅ Context awareness — Include relevant project-specific details
- ✅ Versioning — Track prompt changes over time
- ✅ Examples — Provide sample inputs/outputs

### For Commands
- ✅ Fast execution — Under 30 seconds when possible
- ✅ Clear naming — Verb-based, self-explanatory
- ✅ Confirmation prompts — For destructive operations
- ✅ Rich output — Helpful success/error messages

### For Plugins
- ✅ Robust auth — Secure credential management
- ✅ Rate limiting — Respect API quotas
- ✅ Offline handling — Graceful degradation without network
- ✅ Documentation — Clear setup and configuration steps

---

## 🤝 Contributing

We welcome contributions that meet our production-grade standards. Whether you're adding a new agent, prompt, command, or plugin, ensure it's:

1. **Battle-tested** — Used in production environments
2. **Well-documented** — Clear README with usage examples
3. **Maintainable** — Clean code, clear abstractions
4. **Secure** — No hardcoded secrets, proper input validation
5. **Performant** — Optimized for real-world scale

### Contribution Process

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-agent`)
3. Add your component in the appropriate directory
4. Include comprehensive documentation
5. Submit a pull request with detailed description

---

## 📚 Resources

### Official Documentation
- [Claude Code SDK Documentation](https://github.com/anthropics/claude-code)
- [Claude Agent SDK Guide](https://github.com/anthropics/claude-agent-sdk)
- [MCP Protocol Specification](https://modelcontextprotocol.io)

### Community
- [Claude Code GitHub Discussions](https://github.com/anthropics/claude-code/discussions)
- [Anthropic Discord](https://discord.gg/anthropic)

---

## 📊 Marketplace Stats

_Track the growth of our marketplace:_

- **🤖 Agents:** 4 (2 meta-agents, 2 implementation agents)
- **🔌 Plugins:** 2 (engineering-orchestrator, database-architect)
- **✍️ Prompts:** 0 (launching soon)
- **⚡ Commands:** 0 (launching soon)

> Last updated: 2025-12-08

---

## 🌟 Showcase

_Featured implementations using this marketplace:_

**Have a production deployment?** Share your story! Submit a PR adding your company/project to this section.

---

## 🛣️ Roadmap

### Q1 2025
- [x] Engineering orchestrator (progressive disclosure + agent coordination)
- [x] Database architecture suite (advisor + SQL Server + Cosmos DB)
- [x] Reference materials pattern (efficient context usage)
- [ ] Core prompt library for common workflows
- [ ] Essential slash commands for daily tasks

### Q2 2025
- [ ] Frontend architecture agents (React, Angular, Blazor)
- [ ] Backend API design agents (REST, GraphQL, gRPC)
- [ ] Security and compliance agents
- [ ] CI/CD and observability plugin integrations

### Future
- [ ] Performance optimization toolkit
- [ ] Multi-language support expansion
- [ ] Agent orchestration analytics
- [ ] Plugin marketplace discovery

---

## 📝 License

MIT License — see [LICENSE](LICENSE) for details.

Built with ⚡ by engineers who ship code daily.

---

## 🙏 Acknowledgments

Inspired by the Claude Code community and built for teams who demand production-grade tooling. Special thanks to everyone contributing to the Claude Code SDK ecosystem.

**Ready to supercharge your Claude Code workflow?** Star this repo and watch for updates as we add more production-grade components.
