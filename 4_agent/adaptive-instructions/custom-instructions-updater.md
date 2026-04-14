# Custom Instructions Updater

The Custom Instructions Updater (also known as Adaptive Instructions Updater) is a rule-based system that uses pre-defined scenario descriptions to select the most contextually relevant system instructions via embedding similarity matching.

## Overview

Unlike the [Default Instructions Updater](default-instructions-updater.md) which generates instructions using an LLM, the Custom Instructions Updater:

1. **Pre-defines rules** mapping scenario descriptions to instructions
2. **Embeds rule descriptions** into a vector index
3. **Embeds user messages** at runtime
4. **Finds the best match** using cosine similarity
5. **Returns matching instructions** without calling an LLM

This approach is **deterministic**, **cost-effective**, and **predictable** when you know your conversation scenarios upfront.

## How It Works

### Rule Matching Flow

```
User Message
    ↓
Embed message (cheap, ~$0.0001)
    ↓
Compare to all rule embeddings
    ↓
Find highest similarity score
    ↓
Score ≥ MinSimilarity?
    ├─ Yes → Return matched rule's instructions
    └─ No → Return null (keep current instructions)
```

### Example Scenario

**Defined Rules**:
1. "user is asking about billing or payments" → billing_instructions
2. "user is reporting a bug or technical issue" → tech_support_instructions
3. "user wants to request a new feature" → feature_request_instructions

**User Message**: "My credit card keeps getting declined"

**Process**:
- Embed message: `[0.23, 0.89, ...]`
- Rule 1 similarity: **0.87** ← Best match
- Rule 2 similarity: 0.45
- Rule 3 similarity: 0.12

**Result**: Return `billing_instructions`

## Implementation

### Create Custom Updater

The Custom Instructions Updater must be implemented in code (not available in Unity Inspector):

```csharp
using Glitch9.AIDevKit.Agents;

public class RuleBasedAgent : AgentBehaviour
{
    protected override void Start()
    {
        base.Start();
        
        // 1. Create settings
        var settings = new CustomAdaptiveSettings
        {
            EmbeddingModel = "text-embedding-3-small",
            MinSimilarity = 0.70f,
            Rules = new List<InstructionsRule>
            {
                new InstructionsRule(
                    description: "user is asking about billing, payments, or subscription",
                    instructions: GetBillingInstructions()
                ),
                new InstructionsRule(
                    description: "user is reporting a bug, error, or technical issue",
                    instructions: GetTechSupportInstructions()
                ),
                new InstructionsRule(
                    description: "user wants to request a new feature or enhancement",
                    instructions: GetFeatureRequestInstructions()
                )
            }
        };
        
        // 2. Create updater
        var updater = new AdaptiveInstructionsUpdater(settings, Logger);
        
        // 3. Set on agent
        // Note: Requires agent to be initialized
        // Agent.SetInstructionsUpdater(updater);
    }
    
    private string GetBillingInstructions()
    {
        return @"You are a billing support specialist.
Be empathetic and solution-focused.
Always verify the user's account status before providing advice.
Escalate to human support if dealing with charges or refunds.";
    }
    
    private string GetTechSupportInstructions()
    {
        return @"You are a technical support specialist.
Be methodical and patient.
Ask clarifying questions to understand the issue.
Provide step-by-step troubleshooting guidance.
Request logs or screenshots if needed.";
    }
    
    private string GetFeatureRequestInstructions()
    {
        return @"You are a product feedback specialist.
Be enthusiastic and appreciative.
Ask about the user's use case and pain points.
Explain the feature request process.
Thank them for their input.";
    }
}
```

### Settings Interface

Implement `IAdaptiveInstructionsUpdaterSettings`:

```csharp
public class CustomAdaptiveSettings : IAdaptiveInstructionsUpdaterSettings
{
    public Model EmbeddingModel { get; set; }
    public float MinSimilarity { get; set; } = 0.70f;
    public List<InstructionsRule> Rules { get; set; } = new();
    public int MinMessageLength { get; set; } = 10;
    public int MinQuestionLength { get; set; } = 5;
}
```

## Configuration

### Embedding Model

Model used to embed both rule descriptions and user messages.

**Recommended**:
- `text-embedding-3-small` - Fast, cost-effective, good quality
- `text-embedding-ada-002` - Reliable, widely compatible

```csharp
var settings = new CustomAdaptiveSettings
{
    EmbeddingModel = "text-embedding-3-small"
};
```

### Min Similarity Threshold

Minimum cosine similarity (0-1) required to accept a match.

| Threshold | Behavior | Use Case |
|-----------|----------|----------|
| 0.60-0.65 | Very permissive | Broad topic matching |
| 0.70-0.75 | Balanced | General purpose (recommended) |
| 0.80-0.85 | Strict | Precise scenario matching |

**Default**: 0.70

```csharp
var settings = new CustomAdaptiveSettings
{
    MinSimilarity = 0.72f  // Slightly more strict than default
};
```

When similarity falls below threshold, the updater returns `null` (keeps current instructions).

### Rules Definition

Each rule consists of:

- **Description**: Scenario description (used for embedding matching)
- **Instructions**: System instructions to apply when matched

```csharp
var rule = new InstructionsRule(
    description: "user is asking about account security or password reset",
    instructions: "You are a security support specialist. Verify user identity..."
);
```

## Rule Design Best Practices

### 1. Use Clear, Semantic Descriptions

**Good**:
```csharp
new InstructionsRule(
    "user is asking about pricing, plans, or upgrading their subscription",
    pricingInstructions
)
```

**Bad**:
```csharp
new InstructionsRule(
    "pricing",  // Too vague
    pricingInstructions
)
```

### 2. Avoid Overlapping Rules

**Problematic**:
```csharp
// These will compete and cause confusion
new InstructionsRule("user has a billing question", instructions1),
new InstructionsRule("user is asking about payment", instructions2)
```

**Better**:
```csharp
// Combine into one clear rule
new InstructionsRule(
    "user has questions about billing, payments, or invoices",
    billingInstructions
)
```

### 3. Cover Distinct Scenarios

```csharp
var rules = new List<InstructionsRule>
{
    // Onboarding
    new InstructionsRule(
        "user is new and needs help getting started or onboarding",
        onboardingInstructions
    ),
    
    // Technical support
    new InstructionsRule(
        "user is experiencing errors, bugs, or technical issues",
        techSupportInstructions
    ),
    
    // Integrations
    new InstructionsRule(
        "user wants to connect third-party services or APIs",
        integrationsInstructions
    ),
    
    // Account management
    new InstructionsRule(
        "user needs help with account settings, profile, or team management",
        accountInstructions
    )
};
```

### 4. Write Specific Instructions

Each rule's instructions should be tailored to that scenario:

```csharp
new InstructionsRule(
    "user is experiencing slow performance or loading times",
    instructions: @"You are a performance optimization specialist.

First, gather diagnostic information:
- What action is slow? (page load, search, etc.)
- Browser and device information
- When did this start?

Common causes:
- Large datasets
- Browser cache issues
- Network connectivity

Provide systematic troubleshooting steps.
Escalate to engineering if issue persists after basic troubleshooting."
)
```

## Advanced Usage

### Dynamic Rule Management

Add or update rules at runtime:

```csharp
public class DynamicRuleManager : MonoBehaviour
{
    private AdaptiveInstructionsUpdater updater;
    private CustomAdaptiveSettings settings;
    
    private void Start()
    {
        settings = new CustomAdaptiveSettings
        {
            EmbeddingModel = "text-embedding-3-small",
            MinSimilarity = 0.70f,
            Rules = new List<InstructionsRule>()
        };
        
        updater = new AdaptiveInstructionsUpdater(settings, null);
        
        // Start with basic rules
        AddDefaultRules();
    }
    
    public void AddRule(string description, string instructions)
    {
        var newRule = new InstructionsRule(description, instructions);
        settings.Rules.Add(newRule);
        
        // Force index rebuild
        updater.Invalidate();
        
        Debug.Log($"Added rule: {description}");
    }
    
    public void RemoveRule(string description)
    {
        settings.Rules.RemoveAll(r => r.Description == description);
        updater.Invalidate();
    }
    
    private void AddDefaultRules()
    {
        AddRule(
            "user needs general help or navigation assistance",
            "You are a friendly assistant helping users navigate the platform."
        );
    }
}
```

### Conditional Rule Loading

Load different rule sets based on context:

```csharp
public class ContextualRuleLoader : MonoBehaviour
{
    public void LoadRulesForUserType(UserType userType)
    {
        List<InstructionsRule> rules = userType switch
        {
            UserType.FreeUser => GetFreeUserRules(),
            UserType.PaidUser => GetPaidUserRules(),
            UserType.Enterprise => GetEnterpriseUserRules(),
            _ => GetDefaultRules()
        };
        
        var settings = new CustomAdaptiveSettings
        {
            EmbeddingModel = "text-embedding-3-small",
            Rules = rules
        };
        
        // Apply to agent
    }
    
    private List<InstructionsRule> GetFreeUserRules()
    {
        return new List<InstructionsRule>
        {
            new InstructionsRule(
                "user wants premium features or asks about limitations",
                "Explain free tier limits and benefits of upgrading. Be encouraging but not pushy."
            ),
            // ... more free-tier specific rules
        };
    }
}
```

### A/B Testing Rules

Test different instruction sets:

```csharp
public class RuleABTester : MonoBehaviour
{
    private Dictionary<string, List<string>> ruleVariants = new();
    
    public void SetupABTest(string scenario)
    {
        ruleVariants[scenario] = new List<string>
        {
            // Variant A: Concise
            "Be brief and direct. Provide only essential information.",
            
            // Variant B: Detailed
            "Be thorough and educational. Explain context and reasoning.",
            
            // Variant C: Conversational
            "Be friendly and conversational. Use examples and analogies."
        };
    }
    
    public string GetRandomVariant(string scenario)
    {
        if (!ruleVariants.ContainsKey(scenario)) return null;
        
        var variants = ruleVariants[scenario];
        return variants[UnityEngine.Random.Range(0, variants.Count)];
    }
}
```

## Performance Characteristics

### Cost Comparison

| Operation | Default Updater | Custom Updater |
|-----------|-----------------|----------------|
| Setup | No cost | Build index once |
| Per message | Embedding + LLM (if shift) | Embedding only |
| Typical message | $0.0001 - $0.005 | $0.0001 |

### Latency

- **Index build**: ~100ms for 10 rules (one-time)
- **Per message**: ~50ms (embedding) + ~10ms (search)
- **Total**: ~60ms overhead per message

### Scalability

Rules scale linearly:

| Rule Count | Index Build | Per-Message Latency |
|-----------|-------------|---------------------|
| 5 rules | ~50ms | ~60ms |
| 10 rules | ~100ms | ~65ms |
| 20 rules | ~200ms | ~70ms |
| 50 rules | ~500ms | ~85ms |

**Recommendation**: Keep rule count under 20 for optimal performance.

## Debugging

### Log Matching Decisions

```csharp
public class DebugAdaptiveUpdater : AdaptiveInstructionsUpdater
{
    public DebugAdaptiveUpdater(IAdaptiveInstructionsUpdaterSettings settings, ILogger logger)
        : base(settings, logger) { }
    
    public override async UniTask<string> UpdateInstructionsAsync(
        string lastUserMessage, 
        CancellationToken ct = default)
    {
        Debug.Log($"Processing message: {lastUserMessage}");
        
        string result = await base.UpdateInstructionsAsync(lastUserMessage, ct);
        
        if (result != null)
        {
            Debug.Log($"Matched instructions: {result.Substring(0, Math.Min(50, result.Length))}...");
        }
        else
        {
            Debug.Log("No rule matched threshold");
        }
        
        return result;
    }
}
```

### Test Rule Matching

```csharp
private async void TestRuleMatching()
{
    var testMessages = new[]
    {
        "I can't log in to my account",
        "How much does the enterprise plan cost?",
        "The app keeps crashing when I export data",
        "Can you add a dark mode feature?"
    };
    
    foreach (var message in testMessages)
    {
        var instructions = await updater.UpdateInstructionsAsync(message);
        Debug.Log($"Message: {message}");
        Debug.Log($"Matched: {instructions ?? "None"}");
        Debug.Log("---");
    }
}
```

### Analyze Similarity Scores

Extend the updater to expose scores:

```csharp
public class ScoredAdaptiveUpdater : AdaptiveInstructionsUpdater
{
    public Dictionary<string, float> LastScores { get; private set; }
    
    public override async UniTask<string> UpdateInstructionsAsync(
        string lastUserMessage,
        CancellationToken ct = default)
    {
        LastScores = new Dictionary<string, float>();
        
        // ... embed message, compute scores ...
        
        foreach (var (rule, score) in allScores)
        {
            LastScores[rule.Description] = score;
        }
        
        return await base.UpdateInstructionsAsync(lastUserMessage, ct);
    }
}

// Usage
var scores = ((ScoredAdaptiveUpdater)updater).LastScores;
foreach (var (desc, score) in scores.OrderByDescending(s => s.Value))
{
    Debug.Log($"{score:F3} - {desc}");
}
```

## Comparison with Default Updater

| Aspect | Custom Updater | Default Updater |
|--------|----------------|-----------------|
| **Setup effort** | High (define all rules) | Low (just enable) |
| **Flexibility** | Limited to defined rules | Handles any topic |
| **Predictability** | Fully deterministic | LLM-dependent |
| **Cost per message** | ~$0.0001 | ~$0.0001-$0.005 |
| **Latency** | ~60ms | ~60ms-2s |
| **Maintenance** | Manual rule updates | Zero maintenance |
| **Control** | Full control | Limited control |

### When to Use Custom Updater

✅ You have **well-defined conversation scenarios**

✅ You need **predictable, consistent behavior**

✅ You want **zero LLM costs** for instruction updates

✅ You can **maintain and update rules** over time

✅ Your scenarios are **relatively stable** (not constantly changing)

### When to Use Default Updater

✅ You don't know all conversation scenarios upfront

✅ You want **zero-configuration** adaptive behavior

✅ Your topics are **diverse and unpredictable**

✅ You can accept **slight unpredictability** in instructions

✅ Cost optimization is less critical than flexibility

## Best Practices Summary

1. **Start with 5-10 rules** covering your most common scenarios
2. **Use clear, semantic descriptions** (not keywords)
3. **Avoid overlapping rules** that compete
4. **Test similarity scores** for your actual user messages
5. **Monitor match rates** and adjust threshold accordingly
6. **Version control your rules** like any other code
7. **Document each rule's purpose** for team clarity
8. **Review and update rules** based on conversation analytics

## Example: Customer Support Agent

Complete implementation for a customer support scenario:

```csharp
using Glitch9.AIDevKit.Agents;
using System.Collections.Generic;
using UnityEngine;

public class SupportAgentWithRules : AgentBehaviour
{
    protected override void Start()
    {
        base.Start();
        
        var settings = new SupportAdaptiveSettings();
        var updater = new AdaptiveInstructionsUpdater(settings, Logger);
        
        // Set on agent after initialization
    }
}

public class SupportAdaptiveSettings : IAdaptiveInstructionsUpdaterSettings
{
    public Model EmbeddingModel => "text-embedding-3-small";
    public float MinSimilarity => 0.72f;
    public int MinMessageLength => 10;
    public int MinQuestionLength => 5;
    
    public List<InstructionsRule> Rules => new()
    {
        // Authentication issues
        new InstructionsRule(
            "user cannot log in, forgot password, or has authentication issues",
            @"You are a security-focused support specialist.
            Verify the user's identity carefully.
            Guide them through password reset process.
            Check for common issues: caps lock, email typos, expired sessions.
            Never ask for their current password."
        ),
        
        // Billing and payments
        new InstructionsRule(
            "user has questions about billing, payments, subscriptions, or invoices",
            @"You are a billing support specialist.
            Be empathetic about payment issues.
            Verify account and payment status.
            Explain charges clearly.
            Escalate refund requests to human support.
            Offer payment plan options when appropriate."
        ),
        
        // Technical errors
        new InstructionsRule(
            "user is experiencing errors, crashes, bugs, or technical problems",
            @"You are a technical troubleshooting specialist.
            Be methodical and patient.
            Gather: What happened? When? What were you doing?
            Ask for: Browser/device info, error messages, screenshots.
            Provide step-by-step troubleshooting.
            Escalate if issue persists after basic steps."
        ),
        
        // Feature requests
        new InstructionsRule(
            "user wants to request a new feature, suggest an improvement, or provide feedback",
            @"You are an enthusiastic product feedback specialist.
            Thank them for their input.
            Ask about their use case and pain points.
            Explain: feedback is reviewed, but no timeline guarantees.
            Don't make promises about implementation.
            Record details clearly for product team."
        ),
        
        // How-to questions
        new InstructionsRule(
            "user needs help learning how to use a feature or complete a task",
            @"You are a patient education specialist.
            Assess their experience level.
            Provide clear, step-by-step guidance.
            Use screenshots or examples when helpful.
            Check understanding after each major step.
            Encourage questions and exploration."
        ),
        
        // Account management
        new InstructionsRule(
            "user wants to update account settings, manage team members, or change profile",
            @"You are an account management specialist.
            Guide them to the right settings section.
            Warn before any destructive changes.
            Explain permissions and access levels clearly.
            For team management, verify their admin status."
        )
    };
}
```

## See Also

- [Adaptive Instructions Overview](README.md) - Main guide
- [Default Instructions Updater](default-instructions-updater.md) - LLM-driven alternative
- [Agent Profile](../components/agent-profile.md) - Agent configuration
- [Tool Routing](../tools/tool-integrations/tool-routing.md) - Similar embedding-based system for tools
