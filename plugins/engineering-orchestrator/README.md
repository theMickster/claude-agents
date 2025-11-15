# Engineering Orchestrator

**Coordinate complex software engineering tasks by orchestrating specialized agents with deliberate reasoning and architectural coherence.**

## What It Does

The Engineering Orchestrator is a meta-agent that breaks down sophisticated engineering requests into coordinated subtasks and delegates them to specialized agents from the claude-code-collective marketplace. Think of it as a conductor for your development workflow.

## When to Use

Use the orchestrator when your task:
- Spans multiple domains (backend, frontend, database, DevOps)
- Requires architectural decision-making
- Involves coordinating multiple specialized agents
- Needs progressive disclosure of requirements

**Examples:**
- "Build a user authentication system with social login"
- "Optimize our API performance"
- "Migrate from monolith to microservices"
- "Design a multi-region data architecture"

## How It Works

1. **Thinks Deeply** — Uses chain-of-thought reasoning to understand your request
2. **Discovers Agents** — Finds the right specialized agents from the marketplace
3. **Plans Deliberately** — Sequences tasks optimally with clear dependencies
4. **Coordinates Precisely** — Invokes agents with rich context
5. **Integrates Coherently** — Ensures all components work together seamlessly

## Key Features

✅ **Progressive Disclosure** — Asks strategic questions to uncover requirements
✅ **Agent Discovery** — Dynamically finds available agents in your installation
✅ **Deliberate Reasoning** — Shows its thinking process transparently
✅ **Rich Context** — Provides detailed context to specialized agents
✅ **Quality Validation** — Ensures production-grade standards throughout

## Installation

This agent is part of the [claude-code-collective](../../.claude-plugin/marketplace.json) marketplace.

Install via Claude Code:
```bash
# The orchestrator is automatically available when the plugin is installed
# It discovers other agents dynamically from your marketplace.json
```

## Example Usage

```
You: "I need to build a real-time notification system for our e-commerce platform"

Orchestrator:
<thinking>
- Multi-layer task: backend, database, frontend
- Discovering available agents from marketplace...
- Need: database schema, SignalR backend, React integration
- Will coordinate: sql-server-architect → backend impl → frontend impl
</thinking>

"I'll orchestrate this real-time notification system. Let me first understand:
1. Database: SQL Server or Cosmos DB?
2. Notification types: Order updates, inventory alerts, or both?
3. Scale: How many concurrent users?

Based on your answers, I'll coordinate the database schema design,
SignalR hub implementation, and React notification components."
```

## Agent Coordination

The orchestrator works with specialized agents like:

- **[sql-server-architect](../database-architect/agents/sql-server-architect.md)** — SQL Server schema and optimization
- **[cosmosdb-architect](../database-architect/agents/cosmosdb-architect.md)** — Cosmos DB design and performance
- More agents added as the marketplace grows...

## Production Standards

Every orchestrated solution meets [CLAUDE.md](../../.claude/CLAUDE.md) standards:
- Production-ready code quality
- Comprehensive error handling
- Security best practices
- Performance optimization
- Complete documentation

## Learn More

- [Full Agent Definition](./engineering-orchestrator.md)
- [Marketplace Registry](../../.claude-plugin/marketplace.json)
- [Project Guide](../../.claude/CLAUDE.md)

---

**Built for teams who ship production code daily.** 🚀
