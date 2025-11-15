# Prompt Template Systems

## Template Architecture

### Basic Template Structure

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class PromptTemplate
{
    protected string _template;
    protected List<string> _variables;

    public PromptTemplate(string templateString, List<string> variables = null)
    {
        _template = templateString;
        _variables = variables ?? new List<string>();
    }

    public virtual string Render(Dictionary<string, object> variables)
    {
        var missing = _variables.Except(variables.Keys).ToList();
        if (missing.Any())
        {
            throw new ArgumentException($"Missing required variables: {string.Join(", ", missing)}");
        }

        string result = _template;
        foreach (var (key, value) in variables)
        {
            result = result.Replace($"{{{key}}}", value?.ToString() ?? "");
        }

        return result;
    }
}

// Usage
var template = new PromptTemplate(
    templateString: "Translate {text} from {sourceLang} to {targetLang}",
    variables: new List<string> { "text", "sourceLang", "targetLang" }
);

string prompt = template.Render(new Dictionary<string, object>
{
    ["text"] = "Hello world",
    ["sourceLang"] = "English",
    ["targetLang"] = "Spanish"
});
```

### Conditional Templates

```csharp
using System;
using System.Collections.Generic;
using System.Text.RegularExpressions;
using System.Linq;

public class ConditionalTemplate : PromptTemplate
{
    public ConditionalTemplate(string templateString, List<string> variables = null)
        : base(templateString, variables)
    {
    }

    public override string Render(Dictionary<string, object> variables)
    {
        string result = _template;

        string ifPattern = @"\{\{#if (\w+)\}\}(.*?)\{\{/if\}\}";
        result = Regex.Replace(result, ifPattern, match =>
        {
            string varName = match.Groups[1].Value;
            string content = match.Groups[2].Value;
            return variables.ContainsKey(varName) && IsTruthy(variables[varName])
                ? content
                : "";
        }, RegexOptions.Singleline);

        string eachPattern = @"\{\{#each (\w+)\}\}(.*?)\{\{/each\}\}";
        result = Regex.Replace(result, eachPattern, match =>
        {
            string varName = match.Groups[1].Value;
            string content = match.Groups[2].Value;

            if (!variables.ContainsKey(varName))
                return "";

            var items = variables[varName] as IEnumerable<object>;
            if (items == null)
                return "";

            return string.Join("\n", items.Select(item =>
                content.Replace("{{this}}", item?.ToString() ?? "")
            ));
        }, RegexOptions.Singleline);

        foreach (var (key, value) in variables)
        {
            result = result.Replace($"{{{key}}}", value?.ToString() ?? "");
        }

        return result;
    }

    private bool IsTruthy(object value)
    {
        if (value == null) return false;
        if (value is bool b) return b;
        if (value is string s) return !string.IsNullOrEmpty(s);
        if (value is int i) return i != 0;
        return true;
    }
}

// Usage
var template = new ConditionalTemplate(@"
Analyze the following text:
{text}

{{#if includeSentiment}}
Provide sentiment analysis.
{{/if}}

{{#if includeEntities}}
Extract named entities.
{{/if}}

{{#if examples}}
Reference examples:
{{#each examples}}
- {{this}}
{{/each}}
{{/if}}
");
```

### Modular Template Composition

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class ModularTemplate
{
    private Dictionary<string, string> _components;

    public ModularTemplate()
    {
        _components = new Dictionary<string, string>();
    }

    public void RegisterComponent(string name, string template)
    {
        _components[name] = template;
    }

    public string Render(List<string> structure, Dictionary<string, object> variables)
    {
        var parts = new List<string>();

        foreach (var componentName in structure)
        {
            if (_components.ContainsKey(componentName))
            {
                string component = _components[componentName];

                foreach (var (key, value) in variables)
                {
                    component = component.Replace($"{{{key}}}", value?.ToString() ?? "");
                }

                parts.Add(component);
            }
        }

        return string.Join("\n\n", parts);
    }
}

// Usage
var builder = new ModularTemplate();

builder.RegisterComponent("system", "You are a {role}.");
builder.RegisterComponent("context", "Context: {context}");
builder.RegisterComponent("instruction", "Task: {task}");
builder.RegisterComponent("examples", "Examples:\n{examples}");
builder.RegisterComponent("input", "Input: {input}");
builder.RegisterComponent("format", "Output format: {format}");

// Compose different templates for different scenarios
string basicPrompt = builder.Render(
    new List<string> { "system", "instruction", "input" },
    new Dictionary<string, object>
    {
        ["role"] = "helpful assistant",
        ["instruction"] = "Summarize the text",
        ["input"] = "..."
    }
);

string advancedPrompt = builder.Render(
    new List<string> { "system", "context", "examples", "instruction", "input", "format" },
    new Dictionary<string, object>
    {
        ["role"] = "expert analyst",
        ["context"] = "Financial analysis",
        ["examples"] = "...",
        ["instruction"] = "Analyze sentiment",
        ["input"] = "...",
        ["format"] = "JSON"
    }
);
```

## Common Template Patterns

### Classification Template

```csharp
public static class TemplateConstants
{
    public const string CLASSIFICATION_TEMPLATE = @"
Classify the following {contentType} into one of these categories: {categories}

{{#if description}}
Category descriptions:
{description}
{{/if}}

{{#if examples}}
Examples:
{examples}
{{/if}}

{contentType}: {input}

Category:";
}
```

### Extraction Template

```csharp
public const string EXTRACTION_TEMPLATE = @"
Extract structured information from the {contentType}.

Required fields:
{fieldDefinitions}

{{#if examples}}
Example extraction:
{examples}
{{/if}}

{contentType}: {input}

Extracted information (JSON):";
```

### Generation Template

```csharp
public const string GENERATION_TEMPLATE = @"
Generate {outputType} based on the following {inputType}.

Requirements:
{requirements}

{{#if style}}
Style: {style}
{{/if}}

{{#if constraints}}
Constraints:
{constraints}
{{/if}}

{{#if examples}}
Examples:
{examples}
{{/if}}

{inputType}: {input}

{outputType}:";
```

### Transformation Template

```csharp
public const string TRANSFORMATION_TEMPLATE = @"
Transform the input {sourceFormat} to {targetFormat}.

Transformation rules:
{rules}

{{#if examples}}
Example transformations:
{examples}
{{/if}}

Input {sourceFormat}:
{input}

Output {targetFormat}:";
```

## Advanced Features

### Template Inheritance

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class TemplateRegistry
{
    private Dictionary<string, Dictionary<string, string>> _templates;

    public TemplateRegistry()
    {
        _templates = new Dictionary<string, Dictionary<string, string>>();
    }

    public void Register(string name, Dictionary<string, string> template, string parent = null)
    {
        if (parent != null && _templates.ContainsKey(parent))
        {
            var baseTemplate = _templates[parent];
            template = MergeTemplates(baseTemplate, template);
        }

        _templates[name] = template;
    }

    private Dictionary<string, string> MergeTemplates(
        Dictionary<string, string> parent,
        Dictionary<string, string> child)
    {
        var merged = new Dictionary<string, string>(parent);
        foreach (var (key, value) in child)
        {
            merged[key] = value;
        }
        return merged;
    }

    public Dictionary<string, string> GetTemplate(string name)
    {
        return _templates.GetValueOrDefault(name);
    }
}

// Usage
var registry = new TemplateRegistry();

registry.Register("base_analysis", new Dictionary<string, string>
{
    ["system"] = "You are an expert analyst.",
    ["format"] = "Provide analysis in structured format."
});

registry.Register("sentiment_analysis", new Dictionary<string, string>
{
    ["instruction"] = "Analyze sentiment",
    ["format"] = "Provide sentiment score from -1 to 1."
}, parent: "base_analysis");
```

### Variable Validation

```csharp
using System;
using System.Collections.Generic;

public class VariableSchema
{
    public Type Type { get; set; }
    public int? Min { get; set; }
    public int? Max { get; set; }
    public List<object> Choices { get; set; }
}

public class ValidatedTemplate
{
    private string _template;
    private Dictionary<string, VariableSchema> _schema;

    public ValidatedTemplate(string template, Dictionary<string, VariableSchema> schema)
    {
        _template = template;
        _schema = schema;
    }

    public void ValidateVars(Dictionary<string, object> variables)
    {
        foreach (var (varName, varSchema) in _schema)
        {
            if (variables.ContainsKey(varName))
            {
                object value = variables[varName];

                if (varSchema.Type != null && value.GetType() != varSchema.Type)
                {
                    throw new ArgumentException($"{varName} must be {varSchema.Type.Name}");
                }

                if (varSchema.Min.HasValue && value is int intVal && intVal < varSchema.Min.Value)
                {
                    throw new ArgumentException($"{varName} must be >= {varSchema.Min.Value}");
                }

                if (varSchema.Max.HasValue && value is int intVal2 && intVal2 > varSchema.Max.Value)
                {
                    throw new ArgumentException($"{varName} must be <= {varSchema.Max.Value}");
                }

                if (varSchema.Choices != null && !varSchema.Choices.Contains(value))
                {
                    throw new ArgumentException(
                        $"{varName} must be one of {string.Join(", ", varSchema.Choices)}"
                    );
                }
            }
        }
    }

    public string Render(Dictionary<string, object> variables)
    {
        ValidateVars(variables);

        string result = _template;
        foreach (var (key, value) in variables)
        {
            result = result.Replace($"{{{key}}}", value?.ToString() ?? "");
        }

        return result;
    }
}

// Usage
var template = new ValidatedTemplate(
    template: "Summarize in {length} words with {tone} tone",
    schema: new Dictionary<string, VariableSchema>
    {
        ["length"] = new VariableSchema
        {
            Type = typeof(int),
            Min = 10,
            Max = 500
        },
        ["tone"] = new VariableSchema
        {
            Type = typeof(string),
            Choices = new List<object> { "formal", "casual", "technical" }
        }
    }
);
```

### Template Caching

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class CachedTemplate
{
    private string _template;
    private Dictionary<int, string> _cache;

    public CachedTemplate(string template)
    {
        _template = template;
        _cache = new Dictionary<int, string>();
    }

    public string Render(Dictionary<string, object> variables, bool useCache = true)
    {
        if (useCache)
        {
            int cacheKey = GetCacheKey(variables);
            if (_cache.ContainsKey(cacheKey))
            {
                return _cache[cacheKey];
            }
        }

        string result = _template;
        foreach (var (key, value) in variables)
        {
            result = result.Replace($"{{{key}}}", value?.ToString() ?? "");
        }

        if (useCache)
        {
            int cacheKey = GetCacheKey(variables);
            _cache[cacheKey] = result;
        }

        return result;
    }

    private int GetCacheKey(Dictionary<string, object> variables)
    {
        unchecked
        {
            int hash = 17;
            foreach (var (key, value) in variables.OrderBy(x => x.Key))
            {
                hash = hash * 31 + key.GetHashCode();
                hash = hash * 31 + (value?.GetHashCode() ?? 0);
            }
            return hash;
        }
    }

    public void ClearCache()
    {
        _cache.Clear();
    }
}
```

## Multi-Turn Templates

### Conversation Template

```csharp
using System;
using System.Collections.Generic;
using System.Linq;

public class Message
{
    public string Role { get; set; }
    public string Content { get; set; }
}

public class ConversationTemplate
{
    private string _systemPrompt;
    private List<Message> _history;

    public ConversationTemplate(string systemPrompt)
    {
        _systemPrompt = systemPrompt;
        _history = new List<Message>();
    }

    public void AddUserMessage(string message)
    {
        _history.Add(new Message { Role = "user", Content = message });
    }

    public void AddAssistantMessage(string message)
    {
        _history.Add(new Message { Role = "assistant", Content = message });
    }

    public List<Message> RenderForApi()
    {
        var messages = new List<Message>
        {
            new Message { Role = "system", Content = _systemPrompt }
        };
        messages.AddRange(_history);
        return messages;
    }

    public string RenderAsText()
    {
        var result = $"System: {_systemPrompt}\n\n";
        foreach (var msg in _history)
        {
            string role = char.ToUpper(msg.Role[0]) + msg.Role.Substring(1);
            result += $"{role}: {msg.Content}\n\n";
        }
        return result;
    }
}
```

### State-Based Templates

```csharp
using System;
using System.Collections.Generic;

public class StatefulTemplate
{
    private Dictionary<string, object> _state;
    private Dictionary<string, string> _templates;

    public StatefulTemplate()
    {
        _state = new Dictionary<string, object>();
        _templates = new Dictionary<string, string>();
    }

    public void SetState(Dictionary<string, object> updates)
    {
        foreach (var (key, value) in updates)
        {
            _state[key] = value;
        }
    }

    public void RegisterStateTemplate(string stateName, string template)
    {
        _templates[stateName] = template;
    }

    public string Render()
    {
        string currentState = _state.GetValueOrDefault("currentState")?.ToString() ?? "default";

        if (!_templates.ContainsKey(currentState))
        {
            throw new InvalidOperationException($"No template for state: {currentState}");
        }

        string template = _templates[currentState];
        foreach (var (key, value) in _state)
        {
            template = template.Replace($"{{{key}}}", value?.ToString() ?? "");
        }

        return template;
    }
}

// Usage for multi-step workflows
var workflow = new StatefulTemplate();

workflow.RegisterStateTemplate("init", @"
Welcome! Let's {task}.
What is your {firstInput}?
");

workflow.RegisterStateTemplate("processing", @"
Thanks! Processing {firstInput}.
Now, what is your {secondInput}?
");

workflow.RegisterStateTemplate("complete", @"
Great! Based on:
- {firstInput}
- {secondInput}

Here's the result: {result}
");
```

## Best Practices

1. **Keep It DRY**: Use templates to avoid repetition
2. **Validate Early**: Check variables before rendering
3. **Version Templates**: Track changes like code
4. **Test Variations**: Ensure templates work with diverse inputs
5. **Document Variables**: Clearly specify required/optional variables
6. **Use Type Hints**: Make variable types explicit
7. **Provide Defaults**: Set sensible default values where appropriate
8. **Cache Wisely**: Cache static templates, not dynamic ones

## Template Libraries

### Question Answering

```csharp
public static class QATemplates
{
    public static readonly Dictionary<string, string> Templates = new()
    {
        ["factual"] = @"Answer the question based on the context.

Context: {context}
Question: {question}
Answer:",

        ["multi_hop"] = @"Answer the question by reasoning across multiple facts.

Facts: {facts}
Question: {question}

Reasoning:",

        ["conversational"] = @"Continue the conversation naturally.

Previous conversation:
{history}

User: {question}
Assistant:"
    };
}
```

### Content Generation

```csharp
public static class GenerationTemplates
{
    public static readonly Dictionary<string, string> Templates = new()
    {
        ["blog_post"] = @"Write a blog post about {topic}.

Requirements:
- Length: {wordCount} words
- Tone: {tone}
- Include: {keyPoints}

Blog post:",

        ["product_description"] = @"Write a product description for {product}.

Features: {features}
Benefits: {benefits}
Target audience: {audience}

Description:",

        ["email"] = @"Write a {type} email.

To: {recipient}
Context: {context}
Key points: {keyPoints}

Email:"
    };
}
```

## Performance Considerations

- Pre-compile templates for repeated use
- Cache rendered templates when variables are static
- Minimize string concatenation in loops
- Use efficient string formatting (string interpolation, StringBuilder)
- Profile template rendering for bottlenecks
