# Chain-of-Thought Prompting

## Overview

Chain-of-Thought (CoT) prompting elicits step-by-step reasoning from LLMs, dramatically improving performance on complex reasoning, math, and logic tasks.

## Core Techniques

### Zero-Shot CoT

Add a simple trigger phrase to elicit reasoning:

```csharp
string ZeroShotCoT(string query)
{
    return $"{query}\n\nLet's think step by step:";
}

// Example
string query = "If a train travels 60 mph for 2.5 hours, how far does it go?";
string prompt = ZeroShotCoT(query);

// Model output:
// "Let's think step by step:
// 1. Speed = 60 miles per hour
// 2. Time = 2.5 hours
// 3. Distance = Speed × Time
// 4. Distance = 60 × 2.5 = 150 miles
// Answer: 150 miles"
```

### Few-Shot CoT

Provide examples with explicit reasoning chains:

```csharp
string fewShotExamples = @"
Q: Steve has 5 baseballs. He buys 3 more boxes of baseballs. Each box has 1-dozen balls. How many baseballs does he have now?
A: Let's think step by step:
1. Steve starts with 5 baseballs
2. He buys 3 boxes, each with 1-dozen
3. There are 12 baseballs in a 1-dozen box
4. Balls from boxes: 3 × 12 = 36 balls
4. Total: 5 + 36 = 41 balls
Answer: 41

Q: {userQuery}
A: Let's think step by step:";
```

### Self-Consistency

Generate multiple reasoning paths and take the majority vote:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class SelfConsistencyResult
{
    public string Answer { get; set; }
    public double Confidence { get; set; }
    public List<string> AllResponses { get; set; }
}

async Task<SelfConsistencyResult> SelfConsistencyCoT(string query, int n = 5, double temperature = 0.7)
{
    string prompt = $"{query}\n\nLet's think step by step:";
    var responses = new List<string>();

    for (int i = 0; i < n; i++)
    {
        var response = await openaiClient.CreateChatCompletionAsync(
            model: "gpt-5",
            messages: new[] { new { role = "user", content = prompt } },
            temperature: temperature
        );
        responses.Add(ExtractFinalAnswer(response));
    }

    // Take majority vote
    var answerCounts = responses
        .GroupBy(x => x)
        .OrderByDescending(g => g.Count())
        .First();

    string finalAnswer = answerCounts.Key;
    double confidence = (double)answerCounts.Count() / n;

    return new SelfConsistencyResult
    {
        Answer = finalAnswer,
        Confidence = confidence,
        AllResponses = responses
    };
}
```

## Advanced Patterns

### Least-to-Most Prompting

Break complex problems into simpler subproblems:

```csharp
async Task<string> LeastToMostPrompt(string complexQuery)
{
    // Stage 1: Decomposition
    string decompPrompt = $@"Break down this complex problem into simpler subproblems:

Problem: {complexQuery}

Subproblems:";

    var subproblems = await GetLlmResponse(decompPrompt);

    // Stage 2: Sequential solving
    var solutions = new List<string>();
    string context = "";

    foreach (var subproblem in subproblems)
    {
        string solvePrompt = $@"{context}

Solve this subproblem:
{subproblem}

Solution:";

        string solution = await GetLlmResponse(solvePrompt);
        solutions.Add(solution);
        context += $"\n\nPreviously solved: {subproblem}\nSolution: {solution}";
    }

    // Stage 3: Final integration
    string finalPrompt = $@"Given these solutions to subproblems:
{context}

Provide the final answer to: {complexQuery}

Final Answer:";

    return await GetLlmResponse(finalPrompt);
}
```

### Tree-of-Thought (ToT)

Explore multiple reasoning branches:

```csharp
public class TreeOfThought
{
    private ILlmClient _client;
    private int _maxDepth;
    private int _branchesPerStep;

    public TreeOfThought(ILlmClient llmClient, int maxDepth = 3, int branchesPerStep = 3)
    {
        _client = llmClient;
        _maxDepth = maxDepth;
        _branchesPerStep = branchesPerStep;
    }

    public async Task<string> Solve(string problem)
    {
        // Generate initial thought branches
        var initialThoughts = await GenerateThoughts(problem, depth: 0);

        // Evaluate each branch
        string bestPath = null;
        double bestScore = -1;

        foreach (var thought in initialThoughts)
        {
            var (path, score) = await ExploreBranch(problem, thought, depth: 1);
            if (score > bestScore)
            {
                bestScore = score;
                bestPath = path;
            }
        }

        return bestPath;
    }

    private async Task<List<string>> GenerateThoughts(string problem, string context = "", int depth = 0)
    {
        string prompt = $@"Problem: {problem}
{context}

Generate {_branchesPerStep} different next steps in solving this problem:

1.";

        var response = await _client.Complete(prompt);
        return ParseThoughts(response);
    }

    private async Task<double> EvaluateThought(string problem, string thoughtPath)
    {
        string prompt = $@"Problem: {problem}

Reasoning path so far:
{thoughtPath}

Rate this reasoning path from 0-10 for:
- Correctness
- Likelihood of reaching solution
- Logical coherence

Score:";

        var response = await _client.Complete(prompt);
        return double.Parse(response);
    }
}
```

### Verification Step

Add explicit verification to catch errors:

```csharp
async Task<string> CotWithVerification(string query)
{
    // Step 1: Generate reasoning and answer
    string reasoningPrompt = $"{query}\n\nLet's solve this step by step:";
    string reasoningResponse = await GetLlmResponse(reasoningPrompt);

    // Step 2: Verify the reasoning
    string verificationPrompt = $@"Original problem: {query}

Proposed solution:
{reasoningResponse}

Verify this solution by:
1. Checking each step for logical errors
2. Verifying arithmetic calculations
3. Ensuring the final answer makes sense

Is this solution correct? If not, what's wrong?

Verification:";

    string verification = await GetLlmResponse(verificationPrompt);

    // Step 3: Revise if needed
    if (verification.ToLower().Contains("incorrect") || verification.ToLower().Contains("error"))
    {
        string revisionPrompt = $@"The previous solution had errors:
{verification}

Please provide a corrected solution to: {query}

Corrected solution:";
        return await GetLlmResponse(revisionPrompt);
    }

    return reasoningResponse;
}
```

## Domain-Specific CoT

### Math Problems

```csharp
string mathCotTemplate = @"
Problem: {problem}

Solution:
Step 1: Identify what we know
- {listKnownValues}

Step 2: Identify what we need to find
- {targetVariable}

Step 3: Choose relevant formulas
- {formulas}

Step 4: Substitute values
- {substitution}

Step 5: Calculate
- {calculation}

Step 6: Verify and state answer
- {verification}

Answer: {finalAnswer}
";
```

### Code Debugging

```csharp
string debugCotTemplate = @"
Code with error:
{code}

Error message:
{error}

Debugging process:
Step 1: Understand the error message
- {interpretError}

Step 2: Locate the problematic line
- {identifyLine}

Step 3: Analyze why this line fails
- {rootCause}

Step 4: Determine the fix
- {proposedFix}

Step 5: Verify the fix addresses the error
- {verification}

Fixed code:
{correctedCode}
";
```

### Logical Reasoning

```csharp
string logicCotTemplate = @"
Premises:
{premises}

Question: {question}

Reasoning:
Step 1: List all given facts
{facts}

Step 2: Identify logical relationships
{relationships}

Step 3: Apply deductive reasoning
{deductions}

Step 4: Draw conclusion
{conclusion}

Answer: {finalAnswer}
";
```

## Performance Optimization

### Caching Reasoning Patterns

```csharp
public class ReasoningCache
{
    private Dictionary<string, string> _cache = new();

    public string GetSimilarReasoning(string problem, double threshold = 0.85)
    {
        var problemEmbedding = Embed(problem);

        foreach (var (cachedProblem, reasoning) in _cache)
        {
            double similarity = CosineSimilarity(
                problemEmbedding,
                Embed(cachedProblem)
            );
            if (similarity > threshold)
            {
                return reasoning;
            }
        }

        return null;
    }

    public void AddReasoning(string problem, string reasoning)
    {
        _cache[problem] = reasoning;
    }

    private float[] Embed(string text) => _embeddingService.Embed(text);

    private double CosineSimilarity(float[] a, float[] b)
    {
        double dotProduct = 0;
        double magnitudeA = 0;
        double magnitudeB = 0;

        for (int i = 0; i < a.Length; i++)
        {
            dotProduct += a[i] * b[i];
            magnitudeA += a[i] * a[i];
            magnitudeB += b[i] * b[i];
        }

        return dotProduct / (Math.Sqrt(magnitudeA) * Math.Sqrt(magnitudeB));
    }
}
```

### Adaptive Reasoning Depth

```csharp
async Task<string> AdaptiveCoT(string problem, int initialDepth = 3)
{
    int depth = initialDepth;
    string response = null;

    while (depth <= 10)  // Max depth
    {
        response = await GenerateCot(problem, numSteps: depth);

        // Check if solution seems complete
        if (IsSolutionComplete(response))
        {
            return response;
        }

        depth += 2;  // Increase reasoning depth
    }

    return response;  // Return best attempt
}
```

## Evaluation Metrics

```csharp
public class ReasoningQualityMetrics
{
    public double Coherence { get; set; }
    public double Completeness { get; set; }
    public double Correctness { get; set; }
    public double Efficiency { get; set; }
    public double Clarity { get; set; }
}

ReasoningQualityMetrics EvaluateCotQuality(string reasoningChain)
{
    return new ReasoningQualityMetrics
    {
        Coherence = MeasureLogicalCoherence(reasoningChain),
        Completeness = CheckAllStepsPresent(reasoningChain),
        Correctness = VerifyFinalAnswer(reasoningChain),
        Efficiency = CountUnnecessarySteps(reasoningChain),
        Clarity = RateExplanationClarity(reasoningChain)
    };
}
```

## Best Practices

1. **Clear Step Markers**: Use numbered steps or clear delimiters
2. **Show All Work**: Don't skip steps, even obvious ones
3. **Verify Calculations**: Add explicit verification steps
4. **State Assumptions**: Make implicit assumptions explicit
5. **Check Edge Cases**: Consider boundary conditions
6. **Use Examples**: Show the reasoning pattern with examples first

## Common Pitfalls

- **Premature Conclusions**: Jumping to answer without full reasoning
- **Circular Logic**: Using the conclusion to justify the reasoning
- **Missing Steps**: Skipping intermediate calculations
- **Overcomplicated**: Adding unnecessary steps that confuse
- **Inconsistent Format**: Changing step structure mid-reasoning

## When to Use CoT

**Use CoT for:**

- Math and arithmetic problems
- Logical reasoning tasks
- Multi-step planning
- Code generation and debugging
- Complex decision making

**Skip CoT for:**

- Simple factual queries
- Direct lookups
- Creative writing
- Tasks requiring conciseness
- Real-time, latency-sensitive applications

## Resources

- Benchmark datasets for CoT evaluation
- Pre-built CoT prompt templates
- Reasoning verification tools
- Step extraction and parsing utilities
