# Few-Shot Learning Guide

## Overview

Few-shot learning enables LLMs to perform tasks by providing a small number of examples (typically 1-10) within the prompt. This technique is highly effective for tasks requiring specific formats, styles, or domain knowledge.

## Example Selection Strategies

### 1. Semantic Similarity

Select examples most similar to the input query using embedding-based retrieval.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Example
{
    public string Input { get; set; }
    public string Output { get; set; }
}

public class SemanticExampleSelector
{
    private float[][] _exampleEmbeddings;
    private List<Example> _examples;
    private IEmbeddingService _embeddingService;

    public SemanticExampleSelector(List<Example> examples, IEmbeddingService embeddingService)
    {
        _examples = examples;
        _embeddingService = embeddingService;
        _exampleEmbeddings = examples
            .Select(ex => _embeddingService.Encode(ex.Input))
            .ToArray();
    }

    public List<Example> Select(string query, int k = 3)
    {
        var queryEmbedding = _embeddingService.Encode(query);
        var similarities = _exampleEmbeddings
            .Select((emb, idx) => (Similarity: CosineSimilarity(emb, queryEmbedding), Index: idx))
            .OrderByDescending(x => x.Similarity)
            .Take(k)
            .Select(x => _examples[x.Index])
            .ToList();

        return similarities;
    }

    private float CosineSimilarity(float[] a, float[] b)
    {
        float dotProduct = 0f;
        float normA = 0f;
        float normB = 0f;

        for (int i = 0; i < a.Length; i++)
        {
            dotProduct += a[i] * b[i];
            normA += a[i] * a[i];
            normB += b[i] * b[i];
        }

        return dotProduct / (float)(Math.Sqrt(normA) * Math.Sqrt(normB));
    }
}
```

**Best For**: Question answering, text classification, extraction tasks

### 2. Diversity Sampling

Maximize coverage of different patterns and edge cases.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class DiversityExampleSelector
{
    private float[][] _embeddings;
    private List<Example> _examples;
    private IEmbeddingService _embeddingService;

    public DiversityExampleSelector(List<Example> examples, IEmbeddingService embeddingService)
    {
        _examples = examples;
        _embeddingService = embeddingService;
        _embeddings = examples
            .Select(ex => _embeddingService.Encode(ex.Input))
            .ToArray();
    }

    public List<Example> Select(int k = 5)
    {
        // Simple clustering: Select examples spread across embedding space
        var selected = new List<Example>();
        var used = new HashSet<int>();

        // Start with first example
        selected.Add(_examples[0]);
        used.Add(0);

        // Greedily select most distant examples
        while (selected.Count < k && selected.Count < _examples.Count)
        {
            float maxMinDistance = -1f;
            int bestIdx = -1;

            for (int i = 0; i < _embeddings.Length; i++)
            {
                if (used.Contains(i)) continue;

                float minDistance = float.MaxValue;
                foreach (var idx in used)
                {
                    float distance = EuclideanDistance(_embeddings[i], _embeddings[idx]);
                    minDistance = Math.Min(minDistance, distance);
                }

                if (minDistance > maxMinDistance)
                {
                    maxMinDistance = minDistance;
                    bestIdx = i;
                }
            }

            if (bestIdx >= 0)
            {
                selected.Add(_examples[bestIdx]);
                used.Add(bestIdx);
            }
        }

        return selected;
    }

    private float EuclideanDistance(float[] a, float[] b)
    {
        float sum = 0f;
        for (int i = 0; i < a.Length; i++)
        {
            sum += (a[i] - b[i]) * (a[i] - b[i]);
        }
        return (float)Math.Sqrt(sum);
    }
}
```

**Best For**: Demonstrating task variability, edge case handling

### 3. Difficulty-Based Selection

Gradually increase example complexity to scaffold learning.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class ExampleWithDifficulty : Example
{
    public double Difficulty { get; set; }
}

public class ProgressiveExampleSelector
{
    private List<ExampleWithDifficulty> _examples;

    public ProgressiveExampleSelector(List<ExampleWithDifficulty> examples)
    {
        // Examples should have 'difficulty' scores (0-1)
        _examples = examples.OrderBy(x => x.Difficulty).ToList();
    }

    public List<ExampleWithDifficulty> Select(int k = 3)
    {
        var selected = new List<ExampleWithDifficulty>();
        int step = Math.Max(1, _examples.Count / k);

        for (int i = 0; i < k; i++)
        {
            int idx = Math.Min(i * step, _examples.Count - 1);
            selected.Add(_examples[idx]);
        }

        return selected;
    }
}
```

**Best For**: Complex reasoning tasks, code generation

### 4. Error-Based Selection

Include examples that address common failure modes.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class ExampleWithPatterns : Example
{
    public List<string> Demonstrates { get; set; } = new List<string>();
}

public class ErrorGuidedSelector
{
    private List<ExampleWithPatterns> _examples;
    private List<string> _errorPatterns;

    public ErrorGuidedSelector(List<ExampleWithPatterns> examples, List<string> errorPatterns)
    {
        _examples = examples;
        _errorPatterns = errorPatterns;  // Common mistakes to avoid
    }

    public List<ExampleWithPatterns> Select(string query, int k = 3)
    {
        var selected = new List<ExampleWithPatterns>();

        foreach (var pattern in _errorPatterns.Take(k))
        {
            var matching = _examples
                .Where(ex => ex.Demonstrates.Contains(pattern))
                .FirstOrDefault();

            if (matching != null)
            {
                selected.Add(matching);
            }
        }

        return selected;
    }
}
```

**Best For**: Tasks with known failure patterns, safety-critical applications

## Example Construction Best Practices

### Format Consistency

All examples should follow identical formatting:

```csharp
// Good: Consistent format
var examples = new List<Example>
{
    new Example { Input = "What is the capital of France?", Output = "Paris" },
    new Example { Input = "What is the capital of Germany?", Output = "Berlin" }
};

// Bad: Inconsistent format would mix different structures
// Use a consistent class or dictionary structure for all examples
```

### Input-Output Alignment

Ensure examples demonstrate the exact task you want the model to perform:

```csharp
// Good: Clear input-output relationship
var goodExample = new Example
{
    Input = "Sentiment: The movie was terrible and boring.",
    Output = "Negative"
};

// Bad: Ambiguous relationship
var badExample = new Example
{
    Input = "The movie was terrible and boring.",
    Output = "This review expresses negative sentiment toward the film."
};
```

### Complexity Balance

Include examples spanning the expected difficulty range:

```csharp
var examples = new List<Example>
{
    // Simple case
    new Example { Input = "2 + 2", Output = "4" },

    // Moderate case
    new Example { Input = "15 * 3 + 8", Output = "53" },

    // Complex case
    new Example { Input = "(12 + 8) * 3 - 15 / 5", Output = "57" }
};
```

## Context Window Management

### Token Budget Allocation

Typical distribution for a 4K context window:

```
System Prompt:        500 tokens  (12%)
Few-Shot Examples:   1500 tokens  (38%)
User Input:           500 tokens  (12%)
Response:            1500 tokens  (38%)
```

### Dynamic Example Truncation

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public interface ITokenizer
{
    int CountTokens(string text);
}

public class TokenAwareSelector
{
    private List<Example> _examples;
    private ITokenizer _tokenizer;
    private int _maxTokens;

    public TokenAwareSelector(List<Example> examples, ITokenizer tokenizer, int maxTokens = 1500)
    {
        _examples = examples;
        _tokenizer = tokenizer;
        _maxTokens = maxTokens;
    }

    public List<Example> Select(string query, int k = 5)
    {
        var selected = new List<Example>();
        int totalTokens = 0;

        var candidates = RankByRelevance(query).Take(k);

        foreach (var example in candidates)
        {
            string exampleText = $"Input: {example.Input}\nOutput: {example.Output}\n\n";
            int exampleTokens = _tokenizer.CountTokens(exampleText);

            if (totalTokens + exampleTokens <= _maxTokens)
            {
                selected.Add(example);
                totalTokens += exampleTokens;
            }
            else
            {
                break;
            }
        }

        return selected;
    }

    private IEnumerable<Example> RankByRelevance(string query)
    {
        return _examples;
    }
}
```

## Edge Case Handling

### Include Boundary Examples

```csharp
var edgeCaseExamples = new List<Example>
{
    // Empty input
    new Example { Input = "", Output = "Please provide input text." },

    // Very long input
    new Example { Input = "word word word " + string.Join(" ", Enumerable.Repeat("word", 50)),
                  Output = "Input exceeds maximum length." },

    // Ambiguous input
    new Example { Input = "bank", Output = "Ambiguous: Could refer to financial institution or river bank." },

    // Invalid input
    new Example { Input = "!@#$%", Output = "Invalid input format. Please provide valid text." }
};
```

## Few-Shot Prompt Templates

### Classification Template

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class PromptBuilder
{
    public static string BuildClassificationPrompt(List<Example> examples, string query, List<string> labels)
    {
        var prompt = $"Classify the text into one of these categories: {string.Join(", ", labels)}\n\n";

        foreach (var ex in examples)
        {
            prompt += $"Text: {ex.Input}\nCategory: {ex.Output}\n\n";
        }

        prompt += $"Text: {query}\nCategory:";
        return prompt;
    }
}
```

### Extraction Template

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;

public static string BuildExtractionPrompt(List<Example> examples, string query)
{
    var prompt = "Extract structured information from the text.\n\n";

    foreach (var ex in examples)
    {
        var outputJson = JsonSerializer.Serialize(ex.Output);
        prompt += $"Text: {ex.Input}\nExtracted: {outputJson}\n\n";
    }

    prompt += $"Text: {query}\nExtracted:";
    return prompt;
}
```

### Transformation Template

```csharp
public static string BuildTransformationPrompt(List<Example> examples, string query)
{
    var prompt = "Transform the input according to the pattern shown in examples.\n\n";

    foreach (var ex in examples)
    {
        prompt += $"Input: {ex.Input}\nOutput: {ex.Output}\n\n";
    }

    prompt += $"Input: {query}\nOutput:";
    return prompt;
}
```

## Evaluation and Optimization

### Example Quality Metrics

```csharp
using System;
using System.Collections.Generic;

public class ExampleQualityMetrics
{
    public double Clarity { get; set; }
    public double Representativeness { get; set; }
    public double Difficulty { get; set; }
    public double Uniqueness { get; set; }
}

public class ExampleEvaluator
{
    public ExampleQualityMetrics EvaluateExampleQuality(Example example, List<Example> validationSet)
    {
        return new ExampleQualityMetrics
        {
            Clarity = RateClarity(example),
            Representativeness = CalculateSimilarityToValidation(example, validationSet),
            Difficulty = EstimateDifficulty(example),
            Uniqueness = CalculateUniqueness(example, validationSet)
        };
    }

    private double RateClarity(Example example)
        => throw new NotImplementedException("Implement clarity scoring logic");

    private double CalculateSimilarityToValidation(Example example, List<Example> set)
        => throw new NotImplementedException("Implement validation similarity scoring");

    private double EstimateDifficulty(Example example)
        => throw new NotImplementedException("Implement difficulty estimation");

    private double CalculateUniqueness(Example example, List<Example> others)
        => throw new NotImplementedException("Implement uniqueness calculation");
}
```

### A/B Testing Example Sets

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class TestQuery
{
    public string Input { get; set; }
    public string ExpectedOutput { get; set; }
}

public class ExampleSetComparison
{
    public double SetAAccuracy { get; set; }
    public double SetBAccuracy { get; set; }
    public string Winner { get; set; }
    public double Improvement { get; set; }
}

public interface ILlmClient
{
    string Complete(string prompt);
}

public class ExampleSetTester
{
    private ILlmClient _client;

    public ExampleSetTester(ILlmClient llmClient)
    {
        _client = llmClient;
    }

    public ExampleSetComparison CompareExampleSets(List<Example> setA, List<Example> setB, List<TestQuery> testQueries)
    {
        var resultsA = EvaluateSet(setA, testQueries);
        var resultsB = EvaluateSet(setB, testQueries);

        return new ExampleSetComparison
        {
            SetAAccuracy = resultsA,
            SetBAccuracy = resultsB,
            Winner = resultsA > resultsB ? "A" : "B",
            Improvement = Math.Abs(resultsA - resultsB)
        };
    }

    private double EvaluateSet(List<Example> examples, List<TestQuery> testQueries)
    {
        int correct = 0;
        foreach (var query in testQueries)
        {
            string prompt = BuildPrompt(examples, query.Input);
            string response = _client.Complete(prompt);
            if (response == query.ExpectedOutput)
            {
                correct++;
            }
        }
        return (double)correct / testQueries.Count;
    }

    private string BuildPrompt(List<Example> examples, string input)
    {
        var prompt = "";
        foreach (var ex in examples)
        {
            prompt += $"Input: {ex.Input}\nOutput: {ex.Output}\n\n";
        }
        prompt += $"Input: {input}\nOutput:";
        return prompt;
    }
}
```

## Advanced Techniques

### Meta-Learning (Learning to Select)

Train a small model to predict which examples will be most effective:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class TrainingData
{
    public string Query { get; set; }
    public Example Example { get; set; }
    public bool Success { get; set; }
}

public class LearnedExampleSelector
{
    private List<double> _weights; // Simple linear model weights

    public LearnedExampleSelector()
    {
        _weights = new List<double> { 0.5, 0.1, 0.1, 0.3 }; // Feature weights
    }

    public void Train(List<TrainingData> trainingData)
    {
        // Simplified training: update weights based on success rate
        int successCount = trainingData.Count(x => x.Success);
        double successRate = (double)successCount / trainingData.Count;

        // Adjust weights
        for (int i = 0; i < _weights.Count; i++)
        {
            _weights[i] *= successRate;
        }
    }

    public List<Example> Select(string query, List<Example> candidates, int k = 3)
    {
        var scores = new List<(double Score, Example Example)>();

        foreach (var example in candidates)
        {
            var features = ExtractFeatures(query, example);
            double score = ComputeScore(features);
            scores.Add((score, example));
        }

        return scores
            .OrderByDescending(x => x.Score)
            .Take(k)
            .Select(x => x.Example)
            .ToList();
    }

    private List<double> ExtractFeatures(string query, Example example)
    {
        return new List<double>
        {
            SemanticSimilarity(query, example.Input),
            example.Input.Length / 100.0,
            example.Output.Length / 100.0,
            KeywordOverlap(query, example.Input)
        };
    }

    private double ComputeScore(List<double> features)
    {
        double score = 0;
        for (int i = 0; i < features.Count && i < _weights.Count; i++)
        {
            score += features[i] * _weights[i];
        }
        return score;
    }

    private double SemanticSimilarity(string a, string b)
        => throw new NotImplementedException("Integrate embedding service for semantic similarity");

    private double KeywordOverlap(string query, string text)
        => throw new NotImplementedException("Implement keyword overlap calculation");
}
```

### Adaptive Example Count

Dynamically adjust the number of examples based on task difficulty:

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class AdaptiveExampleSelector
{
    private List<Example> _examples;

    public AdaptiveExampleSelector(List<Example> examples)
    {
        _examples = examples;
    }

    public List<Example> Select(string query, int maxExamples = 5)
    {
        // Start with 1 example and increase until confident
        for (int k = 1; k <= maxExamples; k++)
        {
            var selected = GetTopK(query, k);
            double confidence = EstimateConfidence(query, selected);

            if (confidence > 0.9)
            {
                return selected;
            }
        }

        // Return max_examples if never confident enough
        return GetTopK(query, maxExamples);
    }

    private List<Example> GetTopK(string query, int k)
    {
        return _examples.Take(k).ToList();
    }

    private double EstimateConfidence(string query, List<Example> selected)
    {
        // Simplified confidence estimation
        // In practice, use a lightweight model or heuristic
        return Math.Min(0.5 + (selected.Count * 0.15), 0.95);
    }
}
```

## Common Mistakes

1. **Too Many Examples**: More isn't always better; can dilute focus
2. **Irrelevant Examples**: Examples should match the target task closely
3. **Inconsistent Formatting**: Confuses the model about output format
4. **Overfitting to Examples**: Model copies example patterns too literally
5. **Ignoring Token Limits**: Running out of space for actual input/output

## Resources

- Example dataset repositories
- Pre-built example selectors for common tasks
- Evaluation frameworks for few-shot performance
- Token counting utilities for different models
