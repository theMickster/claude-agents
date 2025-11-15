# Prompt Engineering Patterns Reference

This guide provides detailed implementations of proven prompt engineering patterns. Load this reference when you need deep knowledge about specific pattern implementations.

## Pattern Catalog

### 1. Few-Shot Learning

**When to use:** Teaching specific formats, styles, or behaviors through examples.

**Structure:**

```markdown
[Task description]

## Examples

**Example 1:**
Input: [example input]
Output: [example output]

**Example 2:**
Input: [example input]
Output: [example output]

**Example 3:**
Input: [example input]
Output: [example output]

Now apply this pattern to:
[Actual input]
```

**Best practices:**

- Use 2-4 examples (diminishing returns after 4)
- Examples should demonstrate edge cases
- Maintain consistent format across examples
- Choose diverse examples that cover key variations

**Example Application:**

```markdown
Convert business intelligence queries to SQL for an e-commerce analytics database.

## Examples

**Example 1:**
Input: "Which product categories had the highest return rate last quarter?"
Output: `SELECT c.category_name, 
  ROUND(COUNT(CASE WHEN o.status = 'returned' THEN 1 END) * 100.0 / COUNT(o.order_id), 2) as return_rate_percent
FROM orders o
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id
JOIN categories c ON p.category_id = c.category_id
WHERE o.created_at >= DATE_TRUNC('quarter', CURRENT_DATE - INTERVAL 3 MONTH)
GROUP BY c.category_id, c.category_name
ORDER BY return_rate_percent DESC`

**Example 2:**
Input: "Show me customers who purchased more than 5 times but haven't bought anything in 60 days"
Output: `SELECT cu.customer_id, cu.email, COUNT(o.order_id) as total_orders, MAX(o.created_at) as last_purchase
FROM customers cu
LEFT JOIN orders o ON cu.customer_id = o.customer_id
GROUP BY cu.customer_id, cu.email
HAVING COUNT(o.order_id) > 5 
  AND MAX(o.created_at) < CURRENT_DATE - INTERVAL 60 DAY`

Now convert: "Identify product SKUs with inventory below reorder level that are also trending in searches"
```

---

### 2. Chain-of-Thought (CoT)

**When to use:** Complex reasoning, multi-step problems, debugging, analysis.

**Structure:**

```markdown
[Task description]

## Instructions

Before providing your answer, use <thinking> tags to:

1. Break down the problem
2. Analyze each component
3. Consider edge cases
4. Verify your reasoning

Then provide your final answer.
```

**Best practices:**

- Make thinking process explicit
- Use structured reasoning steps
- Identify assumptions
- Check for logical consistency

**Example Application:**

```markdown
Analyze this code for potential bugs.

## Instructions

Use <thinking> tags to:

1. Trace the execution flow
2. Identify edge cases
3. Check for type errors
4. Analyze error handling
5. Consider race conditions

Then list the bugs found with severity levels.
```

---

### 3. Constitutional AI

**When to use:** Tasks requiring ethical guidelines, safety constraints, or value alignment.

**Structure:**

```markdown
[Role and task description]

## Core Principles

You must adhere to these principles:

1. [Principle 1: e.g., "Prioritize user safety and privacy"]
2. [Principle 2: e.g., "Provide accurate information or admit uncertainty"]
3. [Principle 3: e.g., "Respect intellectual property"]

## Decision Framework

When making choices:

- [Guideline 1]
- [Guideline 2]
- [Guideline 3]

If you encounter [specific situation], you should [specific action].
```

**Best practices:**

- Make principles explicit and clear
- Provide concrete examples of adherence
- Define boundaries clearly
- Include escalation protocols

**Example Application:**

```markdown
You are a security audit agent analyzing code for vulnerabilities.

## Core Principles

1. **User Safety First**: Flag any code that could compromise user data
2. **Accuracy Over Speed**: Take time to thoroughly analyze; false negatives are worse than false positives
3. **Respect Boundaries**: Only analyze provided code; don't suggest accessing systems you shouldn't

## Decision Framework

When finding potential security issues:

- Mark as CRITICAL if it could lead to data breach
- Mark as HIGH if it could be exploited by attackers
- Mark as MEDIUM if it violates best practices
- Mark as LOW if it's a minor concern

If you find credentials or secrets in code: Immediately flag as CRITICAL and recommend using environment variables or secret management systems.
```

---

### 4. Progressive Disclosure

**When to use:** Interactive agents, complex requirements gathering, personalized solutions.

**Structure:**

```markdown
[Role description]

## Process

### Phase 1: Discovery

Ask targeted questions to understand:

- [Question category 1]
- [Question category 2]
- [Question category 3]

Wait for user responses before proceeding.

### Phase 2: Analysis

Based on responses, [analyze/plan approach]

### Phase 3: Solution

[Provide solution]
```

**Best practices:**

- Ask 3-5 strategic questions, not exhaustive lists
- Make questions specific and actionable
- Explain why you're asking
- Use responses to customize solution

**Example Application:**

```markdown
You are a database schema architect.

## Process

### Phase 1: Discovery

Ask these questions to understand requirements:

- What type of data will you store? (users, transactions, content, etc.)
- What are your expected access patterns? (read-heavy, write-heavy, balanced)
- What are your scale requirements? (records, requests/sec, growth rate)
- Do you have any compliance requirements? (GDPR, HIPAA, etc.)

### Phase 2: Analysis

Use <thinking> tags to:

- Evaluate database options (SQL vs NoSQL)
- Consider scaling strategies
- Plan for data relationships
- Address compliance needs

### Phase 3: Schema Design

Present the complete schema with:

- Table/collection definitions
- Indexes for performance
- Constraints for data integrity
- Migration strategy
```

---

### 5. Structured Output

**When to use:** Integration with systems, parsing, consistent formatting, automation.

**Structure:**

````markdown
[Task description]

## Output Format

Your response must follow this exact structure:

```[format]
{
  "field1": "description",
  "field2": "description",
  "field3": ["array", "of", "values"]
}
```
````

## Example Output

```[format]
[Complete example]
```

## Validation Rules

- [Rule 1]
- [Rule 2]
- [Rule 3]

````

**Best practices:**
- Show complete example, not just schema
- Define all required fields
- Specify data types and formats
- Include validation rules
- Handle null/optional fields explicitly

**Example Application:**
```markdown
Analyze git commit messages for quality.

## Output Format

Your response must be valid JSON:

```json
{
  "commit_hash": "string",
  "quality_score": 0-100,
  "issues": [
    {
      "severity": "critical|high|medium|low",
      "category": "formatting|clarity|convention",
      "description": "string",
      "suggestion": "string"
    }
  ],
  "summary": "string"
}
````

## Example Output

```json
{
  "commit_hash": "a1b2c3d",
  "quality_score": 65,
  "issues": [
    {
      "severity": "high",
      "category": "clarity",
      "description": "Commit message is too vague: 'fix stuff'",
      "suggestion": "Specify what was fixed: 'fix: resolve null pointer exception in user authentication'"
    },
    {
      "severity": "medium",
      "category": "convention",
      "description": "Missing conventional commit prefix",
      "suggestion": "Use format: 'type(scope): description' (e.g., 'fix(auth): ...')"
    }
  ],
  "summary": "Commit message needs more specificity and should follow conventional commit format"
}
```

## Validation Rules

- quality_score must be integer 0-100
- issues array can be empty if no issues found
- severity must be one of: critical, high, medium, low
- all string fields must be non-empty

````

---

### 6. Meta-Prompting

**When to use:** Complex prompt design, self-correction, adaptive behavior.

**Structure:**
```markdown
[Role description]

## Meta-Instructions

When interpreting instructions:
- [How to handle ambiguity]
- [How to prioritize conflicting requirements]
- [When to ask for clarification]

## Self-Correction Protocol

After generating output:
1. [Verification step 1]
2. [Verification step 2]
3. [Correction if needed]
````

**Best practices:**

- Make interpretation rules explicit
- Define conflict resolution strategies
- Build in quality checks
- Enable self-correction

**Example Application:**

```markdown
You are an advanced code refactoring agent.

## Meta-Instructions

When interpreting refactoring requests:

- If scope is unclear, ask which files/functions to refactor
- If "clean up" is mentioned, prioritize: correctness > readability > performance
- If requirements conflict (e.g., "make it faster but don't change logic"), clarify priority
- When in doubt, ask rather than assume

## Self-Correction Protocol

After proposing refactoring:

1. Verify: Does this preserve original functionality?
2. Check: Are there any edge cases broken?
3. Validate: Does this follow project's coding standards?
4. Review: Is this actually simpler/better?

If any check fails: Revise the refactoring and note what you corrected.
```

---

## Pattern Combinations

Effective prompts often combine multiple patterns:

### CoT + Few-Shot

```markdown
[Task description]

## Examples with Reasoning

**Example 1:**
<thinking>
[Reasoning for example 1]
</thinking>
Output: [Result]

**Example 2:**
<thinking>
[Reasoning for example 2]
</thinking>
Output: [Result]
```

### Progressive Disclosure + Structured Output

````markdown
## Phase 1: Gather Requirements

[Ask questions]

## Phase 2: Analysis

[Think through approach]

## Phase 3: Deliver Structured Result

```json
{...}
```
````

````

### Constitutional AI + Meta-Prompting
```markdown
## Core Principles
[Ethical guidelines]

## Meta-Instructions
When principles conflict:
1. [Resolution strategy]
2. [Escalation protocol]
````

---

## Selecting the Right Pattern

| Use Case              | Recommended Pattern    | Why                                  |
| --------------------- | ---------------------- | ------------------------------------ |
| Teaching format       | Few-Shot               | Direct examples are most effective   |
| Complex reasoning     | Chain-of-Thought       | Transparency reveals logic errors    |
| Safety-critical       | Constitutional AI      | Explicit principles ensure alignment |
| Unknown requirements  | Progressive Disclosure | Gather context before committing     |
| System integration    | Structured Output      | Machines need predictable formats    |
| Self-improving agents | Meta-Prompting         | Agents need introspection capability |

---

## Anti-Patterns to Avoid

❌ **Over-prompting**: Too many instructions that confuse the model
❌ **Under-specifying**: Vague instructions that yield inconsistent results
❌ **Example mismatch**: Examples that don't match the actual task
❌ **Ignored edge cases**: Not handling unusual inputs
❌ **Format drift**: Examples with inconsistent formats
❌ **Conflicting instructions**: Contradictory requirements without resolution
