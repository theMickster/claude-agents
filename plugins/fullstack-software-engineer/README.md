# Fullstack Software Engineer

**Stop writing code that sucks. Start shipping quality code without unnecessary overhead that delivers value to our customers.**

## What this plugin contains

Language skills, framework patterns, and cross-cutting expertise for modern application development

## What this plugin does

This plugin equips Claude with production-grade expertise across the full application stack. Rather than explaining your tech stack every conversation, Claude automatically applies the right patterns when it detects relevant context.

> _"Building a skill for an agent is like putting together an onboarding guide for a new hire. Instead of building fragmented, custom-designed agents for each use case, anyone can now specialize their agents with composable capabilities."_
>
> — [Anthropic Engineering](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

## Why skills for languages and frameworks

Languages and frameworks are **expertise**, not **workflows**. Claude should apply C# idioms automatically when working with `.cs` files—not require explicit agent invocation.

> _"Skills are model-invoked—Claude autonomously decides when to use them based on your request and the Skill's description."_
>
> — [Claude Code Docs](https://code.claude.com/docs/en/skills)

## Why progressive disclosure

This plugin bundles extensive examples and patterns, but Claude loads only what's needed.

> _"Like a well-organized manual that starts with a table of contents, then specific chapters, skills let Claude load information only as needed... the amount of context that can be bundled into a skill is effectively unbounded."_
>
> — [Anthropic Engineering](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)

## Plugin Structure

```
fullstack-software-engineer/
├── skills/
│   ├── csharp/
│   ├── typescript/
├── agents/
│   ├── fullstack-engineer.md
│   └── code-reviewer.md
└── commands/
    ├── scaffold.md
    └── review.md
```

Skills provide language expertise. Agents use those skills. Commands invoke agents.

## Installation

```bash
/plugin install fullstack-software-engineer@claude-code-collective
```
