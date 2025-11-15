# Prompt Engineer 🎯

**Stop writing prompts that suck. Start shipping prompts that actually work.**

Your LLM is only as good as the prompts you feed it. This agent transforms vague ideas into battle-tested, production-ready prompts that deliver consistent, high-quality outputs—every single time.

No more "hmm, why isn't this working?" 
No more trial-and-error prompt tweaking.
Just prompts that **ship**.

---

## What This Agent Does

Turns your fuzzy requirements into razor-sharp prompts that make LLMs perform like rockstars:

✅ **Creates system prompts** for new agents that work right out of the gate  
✅ **Fixes broken prompts** that give inconsistent or garbage outputs  
✅ **Designs reusable templates** for repeated workflows  
✅ **Implements proven patterns** (Few-Shot, Chain-of-Thought, Constitutional AI)  
✅ **Optimizes for specific models** (Claude Opus/Sonnet/Haiku, GPT-4, etc.)  
✅ **Builds multi-step chains** for complex reasoning tasks  

---

## When to Use

Reach for this agent whenever you're dealing with prompts:

- �� **Building a new agent?** Get a production-grade prompt from the start
- 🔧 **Prompt not working?** The agent diagnoses and fixes the problem
- 📋 **Need consistency?** Create templates that output the same structure every time
- 🧠 **Complex reasoning?** Implement Chain-of-Thought for transparent decision-making
- 🎯 **Model-specific?** Optimize for Claude's long context or GPT-4's function calling
- ⚡ **Multi-step workflows?** Chain prompts together for orchestrated tasks

---

## Why It's Different

### Never Guesses—Asks First
The agent grills you with laser-focused questions until it knows **exactly** what you need. No assumptions. No guesswork.

### Shows Its Work
Uses \`<thinking>\` tags to explain every decision transparently. You see the reasoning behind pattern choices, trade-offs, and optimizations.

### Zero Fluff Policy
You get **complete, copy-paste-ready prompts**—not descriptions of what a prompt should contain. No editing required.

### Battle-Tested Patterns
Implements patterns that actually work in production:
- **Few-Shot Learning** — Teach by example with 2-4 demonstrations
- **Chain-of-Thought** — Explicit reasoning that shows the work
- **Constitutional AI** — Built-in ethical guidelines and safety rails
- **Progressive Disclosure** — Smart requirements gathering
- **Structured Output** — JSON, Markdown, XML—formatted exactly how you need it
- **Meta-Prompting** — Self-correcting prompts that improve over time

### Model-Specific Optimization
Not all models are created equal. This agent tailors prompts for:

**Claude (Opus/Sonnet/Haiku):**
- Leverages 200K token context window
- Structured markdown with clear headings
- \`<thinking>\` tags for reasoning transparency
- Constitutional AI principles
- Positive instructions over negative constraints

**GPT-4:**
- Function calling and tool use
- JSON schemas for structured outputs
- System/user message separation
- Multi-turn conversation optimization

---

## Real Examples

### Example 1: Building a Documentation Agent

**You:** "I need an agent that generates API documentation from code"

**Agent:** 
\`\`\`
<thinking>
- Use case: API docs generation
- Target: Claude Sonnet (speed + quality balance)
- Output: Structured markdown
- Patterns: Few-shot + structured output + progressive disclosure
</thinking>

First, let me understand your setup:
- Which files contain the API routes?
- What framework? (Express, FastAPI, ASP.NET Core?)
- Existing docs to match style?
- Detail level needed?
\`\`\`

**Agent:** [Shows complete prompt with frontmatter, role definition, process steps, and examples]

**Agent:** "This works because it combines progressive disclosure (asks before assuming), chain-of-thought analysis (thinks through auth, errors, models), and structured output (consistent format every time)."

---

### Example 2: Fixing a Broken Prompt

**You:** "This code review prompt sucks: 'Review this code and tell me if it's good.'"

**Agent:**
\`\`\`
<thinking>
- Problem: Way too vague
- Missing: Criteria for "good", output format, context
- Need: Specific review categories, structured output, examples
</thinking>
\`\`\`

**Agent:** [Shows improved prompt with 5 review categories, severity levels, structured output format, and before/after code examples]

**Agent:** "Now you get consistent reviews with severity levels, specific recommendations, and actionable fixes—not just 'looks good' or 'needs work.'"

---

### Example 3: Adding Chain-of-Thought

**You:** "How do I make my agent show its reasoning?"

**Agent:** [Explains CoT pattern, shows \`<thinking>\` tag implementation, provides multiple examples of reasoning transparency]

**Agent:** "Use this when decisions involve trade-offs, multiple options, or complex logic. It builds trust and makes debugging easier."

---

## File Structure

\`\`\`
plugins/prompt-engineer/
├── README.md                          # You are here
├── prompt-engineer.md                 # Agent definition
└── reference/                         # Deep knowledge (loaded on-demand)
    ├── chain-of-thought.md            # CoT patterns, self-consistency, verification
    ├── few-shot-learning.md           # Example selection, semantic similarity, diversity
    ├── output-examples.md             # JSON, Markdown, XML templates
    ├── prompt-optimization.md         # Clarity, efficiency, consistency, robustness
    ├── prompt-patterns.md             # All major patterns with implementations
    ├── prompt-templates.md            # Reusable templates for common tasks
    └── system-prompts.md              # Pre-built prompts for standard roles
\`\`\`

---

## Reference Library

The agent keeps its core prompt lean by loading deep knowledge on-demand from 7 reference files:

| File | What's Inside |
|------|---------------|
| \`chain-of-thought.md\` | CoT reasoning techniques, self-consistency, Tree-of-Thought, adaptive depth |
| \`few-shot-learning.md\` | Example selection strategies, semantic similarity, diversity balancing |
| \`output-examples.md\` | Ready-to-use templates for JSON, Markdown, XML, and structured text |
| \`prompt-optimization.md\` | Techniques for clarity, token efficiency, consistency, robustness |
| \`prompt-patterns.md\` | Detailed implementations of 6+ proven patterns with examples |
| \`prompt-templates.md\` | Reusable templates for classification, extraction, transformation |
| \`system-prompts.md\` | Pre-built prompts for common roles (reviewer, analyzer, generator) |

These are loaded only when needed—keeping the agent fast and context-efficient.

---

## Quality Guarantee

Every prompt this agent creates:

✅ **Works immediately** — No tweaking required  
✅ **Includes all context** — No missing instructions or ambiguity  
✅ **Defines success criteria** — You know what "good" looks like  
✅ **Handles edge cases** — Graceful degradation for unexpected inputs  
✅ **Delivers consistency** — Same input = same output structure  
✅ **Optimized for your model** — Tailored to Claude, GPT-4, or others  
✅ **Follows best practices** — Battle-tested patterns that actually work  

---

## Pro Tips

**1. Be Specific About Your Use Case**  
"I need a prompt for code reviews" → Meh  
"I need a prompt that reviews C# code for security vulnerabilities, outputs JSON with severity levels, and runs in CI/CD pipelines" → 🔥

**2. Name Your Target Model**  
Claude Sonnet and GPT-4 need different optimization strategies. Tell the agent which model you're using.

**3. Share Your Constraints**  
Token limits? Response format requirements? Latency needs? Budget constraints? Share them upfront.

**4. Test and Iterate**  
Use the agent's prompts as starting points. Test with real data. Come back with results for refinement.

**5. Ask for Alternatives**  
Not sure if Few-Shot or Chain-of-Thought is better? Ask for both. Compare. Pick the winner.

---

## What This Agent Does NOT Do

❌ **Doesn't implement code** — It writes prompts, not application logic  
❌ **Doesn't tune model parameters** — No temperature, top-p, or frequency adjustments  
❌ **Doesn't train models** — Prompt engineering, not fine-tuning  
❌ **Doesn't debug your app** — Fixes prompts, not your codebase  

For implementation, coordinate with specialized agents (sql-server-architect, cosmosdb-architect, etc.).

---

## Contributing

Adding new patterns or examples? Keep the quality bar high:

1. **Add pattern implementations** to \`reference/prompt-patterns.md\`
2. **Add output templates** to \`reference/output-examples.md\`
3. **Keep the agent prompt lean** — Reference files are for deep knowledge
4. **Include real-world examples** — Show the pattern's value with actual use cases
5. **Test with real scenarios** — No untested theory. Only battle-tested prompts.

---

## Built For Teams Who Ship 🚀

Part of the [Claude Code Marketplace](../../README.md) — Production-grade agents, prompts, and plugins that enterprise teams trust daily.

**Stop guessing. Start shipping prompts that work.**
