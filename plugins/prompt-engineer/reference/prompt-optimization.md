# Prompt Optimization Guide

## Core Philosophy

**Prompt optimization is iterative refinement based on real failures.** Start with a working prompt, identify specific failure modes, apply targeted fixes, and measure improvement.

## Real Optimization Examples

### Example 1: Vague Instructions → Specific Format

**Problem:** Inconsistent JSON output with extra text

**Before (42% success rate):**
```
Extract key information from this customer support ticket and return it as JSON.

Ticket: Customer says their login isn't working. They tried resetting password but didn't receive email. Account email: user@example.com
```

**Typical failures:**
- "Here's the extracted information: {json...}"
- Missing fields
- Inconsistent field names

**After (96% success rate):**
```
Extract information from the support ticket into this EXACT JSON structure. Return ONLY the JSON, no additional text.

Required fields:
{
  "issue_type": "login|payment|shipping|other",
  "user_email": "email or null",
  "severity": "low|medium|high",
  "summary": "one sentence"
}

Ticket: Customer says their login isn't working. They tried resetting password but didn't receive email. Account email: user@example.com

Output (JSON only):
```

**What changed:**
- Explicit output format with field types
- "ONLY the JSON" constraint
- Example structure shows expected shape
- Clear field options (enums)

**Metrics:**
- Success rate: 42% → 96%
- Avg tokens reduced: 85 → 62
- Retry rate: 38% → 2%

---

### Example 2: Missing Context → Domain Examples

**Problem:** SQL queries with wrong table names and joins

**Before (55% accuracy):**
```
Convert this business question to SQL:
"Show revenue by product category for Q3"
```

**Typical failures:**
- Invents table names: `revenue`, `sales_data`
- Missing joins
- Wrong date filtering

**After (91% accuracy):**
```
Convert business questions to SQL using our e-commerce schema.

Schema:
- orders (id, customer_id, total, created_at, status)
- order_items (order_id, product_id, quantity, price)
- products (id, name, category_id, sku)
- categories (id, name)

Example:
Q: "What were our top 5 products last month?"
A: SELECT p.name, SUM(oi.quantity * oi.price) as revenue
   FROM order_items oi
   JOIN products p ON oi.product_id = p.id
   JOIN orders o ON oi.order_id = o.id
   WHERE o.created_at >= DATE_TRUNC('month', CURRENT_DATE - INTERVAL '1 month')
   GROUP BY p.id, p.name
   ORDER BY revenue DESC
   LIMIT 5;

Now convert:
"Show revenue by product category for Q3"
```

**What changed:**
- Explicit schema with table/column names
- One complete example showing join patterns
- Date filtering pattern

**Metrics:**
- Accuracy: 55% → 91%
- Queries run without errors: 62% → 94%
- Avg generation time: 2.1s → 1.8s

---

### Example 3: Hallucination → Grounding Constraint

**Problem:** Model invents features not in documentation

**Before (68% factually correct):**
```
Based on the Azure Cosmos DB documentation, answer: "How do I configure automatic failover?"

Documentation: {docs_context}
```

**Typical failures:**
- Mentions features from other databases
- Invents configuration options
- Suggests deprecated approaches

**After (94% factually correct):**
```
Answer the question using ONLY information from the provided documentation. If the answer isn't in the docs, say "Not covered in provided documentation."

Documentation:
{docs_context}

Question: "How do I configure automatic failover?"

Rules:
- Only reference features mentioned in the docs above
- Quote specific section titles when possible
- If uncertain, cite the relevant section: "According to [Section Name]..."

Answer:
```

**What changed:**
- "ONLY information from provided documentation" constraint
- Escape hatch for missing info
- Citation requirement

**Metrics:**
- Factual accuracy: 68% → 94%
- Hallucination rate: 22% → 4%
- User trust score: 6.2/10 → 8.7/10

---

### Example 4: Verbose Output → Concise Constraint

**Problem:** Code reviews too long, users don't read them

**Before (avg 850 words):**
```
Review this code change and provide feedback.

{code_diff}
```

**Typical output:**
- 15+ paragraphs
- Repeats obvious things
- Buries critical issues

**After (avg 180 words):**
```
Review this code change. Provide feedback in this format:

**Critical Issues** (blocking):
- [issue if any, or "None"]

**Suggestions** (non-blocking):
- [max 3 items]

**Positive**:
- [1-2 things done well]

Keep each point to ONE sentence. Skip minor style issues.

{code_diff}
```

**What changed:**
- Structured output format
- Explicit length constraints
- "Skip minor style issues" filter

**Metrics:**
- Avg length: 850 → 180 words
- User engagement: 23% → 78% (% who read full review)
- Time saved: 4.2 min → 0.9 min per review

---

## Optimization Patterns

### Pattern 1: Add Output Structure

**When:** Inconsistent formatting, missing fields

**Fix:**
```csharp
// Before
"Summarize this article"

// After
"Summarize this article in exactly this format:
- **Main Point:** [one sentence]
- **Key Facts:** [3 bullet points]
- **Conclusion:** [one sentence]"
```

### Pattern 2: Add Examples

**When:** Wrong format, misunderstood task

**Fix:**
```csharp
// Add 1-2 concrete examples showing input → output
string prompt = $@"Convert questions to API calls.

Example:
Q: 'Show user profile for john@example.com'
API: GET /users/profile?email=john@example.com

Now convert: {userQuestion}";
```

### Pattern 3: Add Constraints

**When:** Too verbose, off-topic, hallucinations

**Fix:**
```csharp
// Before
"Explain {concept}"

// After
"Explain {concept} in 2-3 sentences. Use only information from the docs. Skip historical context."
```

### Pattern 4: Remove Ambiguity

**When:** Inconsistent interpretations

**Fix:**
```csharp
// Before: "Analyze sentiment"
// After: "Classify sentiment as EXACTLY one of: positive, negative, neutral"

// Before: "Find similar items"
// After: "Find 5 most similar items by cosine similarity score > 0.7"
```

### Pattern 5: Add Verification Step

**When:** Math errors, logical mistakes

**Fix:**
```csharp
string prompt = $@"Calculate: {mathProblem}

Then verify your answer by:
1. Checking each arithmetic step
2. Confirming the final number makes sense
3. Trying a different approach to confirm

Show your verification, then provide final answer.";
```

---

## Testing & Measurement

### Quick Test Set

Create 10-20 diverse examples covering:
- Common cases (70%)
- Edge cases (20%)
- Error cases (10%)

### Key Metrics

```csharp
public class PromptMetrics
{
    public double SuccessRate { get; set; }        // % outputs that pass validation
    public double Accuracy { get; set; }           // % factually correct (manual review)
    public int AvgTokens { get; set; }             // Input + output tokens
    public double AvgLatency { get; set; }         // Seconds per request
    public double UserSatisfaction { get; set; }   // 1-10 rating
}

// Example comparison
var before = new PromptMetrics { SuccessRate = 0.65, AvgTokens = 320, AvgLatency = 2.1 };
var after = new PromptMetrics { SuccessRate = 0.94, AvgTokens = 210, AvgLatency = 1.6 };

double improvement = (after.SuccessRate - before.SuccessRate) / before.SuccessRate;
// = 44.6% improvement
```

### A/B Testing (Simple Version)

```csharp
public async Task<string> TestPromptVariants(
    string variantA,
    string variantB,
    List<string> testInputs)
{
    var resultsA = new List<bool>();
    var resultsB = new List<bool>();

    foreach (var input in testInputs)
    {
        string responseA = await llm.Complete(variantA + input);
        string responseB = await llm.Complete(variantB + input);

        resultsA.Add(IsValidOutput(responseA));
        resultsB.Add(IsValidOutput(responseB));
    }

    double successRateA = resultsA.Count(x => x) / (double)resultsA.Count;
    double successRateB = resultsB.Count(x => x) / (double)resultsB.Count;

    return successRateB > successRateA
        ? $"B wins: {successRateB:P0} vs {successRateA:P0}"
        : $"A wins: {successRateA:P0} vs {successRateB:P0}";
}
```

---

## Optimization Workflow

### Step 1: Collect Failures
```
Run prompt on test set → Save all failures → Categorize by type
```

**Failure categories:**
- Wrong format (JSON parsing fails)
- Missing information (empty fields)
- Hallucination (invented facts)
- Off-topic (didn't follow instructions)
- Too verbose/short

### Step 2: Fix One Category at a Time

Don't fix everything at once. Pick the biggest problem:

```csharp
var failuresByType = failures.GroupBy(f => f.Type)
    .OrderByDescending(g => g.Count())
    .First();

// If "wrong format" is #1, add format constraints
// If "hallucination" is #1, add grounding rules
// If "missing info" is #1, add examples
```

### Step 3: Test the Fix

```csharp
// Before applying fix to production
var fixedPrompt = ApplyFix(originalPrompt, targetedFix);
var results = TestOnFailures(fixedPrompt, previousFailures);

if (results.SuccessRate > 0.9)
{
    // Deploy the fix
}
else
{
    // Try different fix
}
```

### Step 4: Monitor in Production

```csharp
public class PromptMonitor
{
    public void LogRequest(string prompt, string response, bool success)
    {
        // Track over time
        metrics.Increment($"prompt.{PromptId}.success", success ? 1 : 0);
        metrics.Timing($"prompt.{PromptId}.latency", responseTime);
        metrics.Gauge($"prompt.{PromptId}.tokens", tokenCount);

        if (!success)
        {
            // Alert if failure rate spikes
            FailureLog.Write(new { prompt, response, timestamp = DateTime.Now });
        }
    }
}
```

---

## Token Reduction Techniques

### Technique 1: Remove Redundancy

```csharp
// Before (42 tokens)
"Please analyze the following text carefully and provide a detailed sentiment analysis of the content."

// After (12 tokens)
"Classify sentiment: positive, negative, or neutral."

// Savings: 71% fewer tokens
```

### Technique 2: Use Abbreviations (After First Mention)

```csharp
// Before
string prompt = @"Natural Language Processing (NLP) helps...
Use Natural Language Processing to...
Natural Language Processing can also...";

// After (define once, abbreviate after)
string prompt = @"Natural Language Processing (NLP) helps...
Use NLP to...
NLP can also...";
```

### Technique 3: Remove Filler Words

```csharp
var fillerWords = new[] {
    "actually", "basically", "essentially", "literally",
    "in order to", "due to the fact that", "at this point in time"
};

string optimized = prompt;
foreach (var filler in fillerWords)
{
    optimized = optimized.Replace(filler, "");
}
```

### Technique 4: Consolidate Instructions

```csharp
// Before (3 separate instructions)
"First, read the input.
Then, analyze it.
Finally, provide output."

// After (single list)
"Steps: 1) Read input 2) Analyze 3) Output results"
```

---

## Common Pitfalls

### ❌ Pitfall 1: Optimizing Without Measuring

```csharp
// DON'T do this
"I think this prompt is better" → deploy

// DO this
var baseline = MeasurePerformance(currentPrompt, testSet);
var improved = MeasurePerformance(newPrompt, testSet);
if (improved.SuccessRate > baseline.SuccessRate) Deploy(newPrompt);
```

### ❌ Pitfall 2: Changing Multiple Things at Once

```csharp
// DON'T: Can't tell what helped
newPrompt = AddExamples(AddStructure(AddConstraints(oldPrompt)));

// DO: Change one thing at a time
var v1 = AddExamples(oldPrompt);
if (Better(v1))
{
    var v2 = AddStructure(v1);
    if (Better(v2)) Deploy(v2);
}
```

### ❌ Pitfall 3: Over-Optimizing for Test Set

```
Test set: 10 examples
Optimize until 100% success
→ Prompt overfits to those 10 examples
→ Fails on real data
```

**Solution:** Keep a separate validation set you don't optimize against.

### ❌ Pitfall 4: Ignoring Latency

```csharp
// Adding examples improves accuracy but adds cost
var before = new { Accuracy = 0.85, Tokens = 150, Cost = "$0.002" };
var after = new { Accuracy = 0.92, Tokens = 450, Cost = "$0.006" };

// Is 7% accuracy improvement worth 3x cost?
// Depends on your use case
```

---

## Version Control

### Simple Versioning

```csharp
public class PromptVersion
{
    public int Version { get; set; }
    public string Content { get; set; }
    public DateTime Created { get; set; }
    public Dictionary<string, double> Metrics { get; set; }
    public string ChangeReason { get; set; }
}

// Example
var history = new List<PromptVersion>
{
    new() {
        Version = 1,
        Content = "Original prompt",
        Metrics = new() { ["success_rate"] = 0.65 },
        ChangeReason = "Initial version"
    },
    new() {
        Version = 2,
        Content = "Added output format",
        Metrics = new() { ["success_rate"] = 0.89 },
        ChangeReason = "Fixed JSON formatting issues"
    },
    new() {
        Version = 3,
        Content = "Added example + format",
        Metrics = new() { ["success_rate"] = 0.94 },
        ChangeReason = "Reduced hallucinations"
    }
};
```

### Rollback Strategy

```csharp
public class PromptManager
{
    private List<PromptVersion> _versions = new();
    private int _currentVersion;

    public void Deploy(string newPrompt, string reason)
    {
        var version = new PromptVersion
        {
            Version = _versions.Count + 1,
            Content = newPrompt,
            Created = DateTime.Now,
            ChangeReason = reason
        };

        _versions.Add(version);
        _currentVersion = version.Version;
    }

    public void Rollback(int toVersion)
    {
        if (toVersion < _versions.Count)
        {
            _currentVersion = toVersion;
            Logger.Warn($"Rolled back prompt to version {toVersion}");
        }
    }

    public string GetCurrent() => _versions[_currentVersion - 1].Content;
}
```

---

## Real-World Tips

1. **Start Simple**: Begin with the shortest prompt that works at all, then refine
2. **One Problem at a Time**: Fix the biggest issue first, don't tackle everything
3. **Measure Everything**: Track success rate, tokens, latency, user satisfaction
4. **Keep Test Cases**: Real failures from production are your best test set
5. **Document Changes**: Note what you changed and why (helps debugging later)
6. **Monitor Production**: Set alerts for sudden drops in success rate
7. **Version Everything**: You'll need to rollback sometimes

---

## Quick Optimization Checklist

When your prompt isn't working well:

- [ ] Is the output format specified explicitly?
- [ ] Are there 1-2 concrete examples?
- [ ] Are instructions unambiguous? (no "might", "could", "some")
- [ ] Are there constraints to prevent hallucination?
- [ ] Is the length/verbosity constrained?
- [ ] Are edge cases covered?
- [ ] Can you remove any filler words?
- [ ] Is the task single-purpose? (not trying to do 3 things)

---

## Further Resources

- Maintain a library of working prompts for common tasks
- Keep a "failure log" of interesting edge cases
- Share prompt improvements across team
- Review prompt metrics weekly
- A/B test major changes before full rollout
