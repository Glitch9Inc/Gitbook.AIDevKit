# Adaptive Instructions

Adaptive Instructions is an experimental feature that dynamically updates the agent's system instructions based on the conversation context, enabling more contextually relevant and specialized responses.

## Overview

Instead of using static system instructions throughout the conversation, Adaptive Instructions allows your agent to:

- **Detect topic shifts** in conversation flow
- **Adapt responses** based on current context
- **Optimize token usage** by regenerating instructions only when needed
- **Specialize behavior** for different conversation scenarios

## How It Works

The system uses embedding-based similarity detection to determine when instructions should be updated:

1. **Conversation flows normally** with current instructions
2. **User message is embedded** (lightweight operation)
3. **Similarity is computed** against cached context
4. **Instructions are updated** if a topic shift is detected
5. **Agent responds** with new contextual instructions

## Available Updaters

AI Dev Kit provides two instruction update strategies:

### Default Instructions Updater (LLM-Driven)

Automatically generates new instructions using an LLM when topic shifts are detected.

**Pros**:
- Zero manual configuration required
- Adapts to any conversation topic
- Instructions are naturally phrased

**Cons**:
- Requires LLM API call on topic shifts (cost)
- Slightly unpredictable output

[Learn more →](default-instructions-updater.md)

### Custom Instructions Updater (Rule-Based)

Uses pre-defined rules with embedding similarity to select the most relevant instructions.

**Pros**:
- Predictable, controllable behavior
- No LLM calls (cost-effective)
- Precise instruction matching

**Cons**:
- Requires manual rule definition
- Limited to predefined scenarios

[Learn more →](custom-instructions-updater.md)

## Configuration

Adaptive Instructions are configured in the `AgentProfile` component:

### Enable the Capability

First, enable the Adaptive Instructions capability in your AgentProfile:

```csharp
// In Unity Inspector: AgentProfile > Capabilities
☑ Adaptive Instructions
```

### Configure Settings

The Adaptive Instructions Settings section appears when the capability is enabled:

- **Enable**: Toggle the feature on/off
- **Embedding Model**: Model used for topic shift detection
- **Generation Model**: Model used to generate new instructions (Default Updater only)
- **Min Message Length**: Skip update for messages shorter than this
- **Min Question Length**: Skip update for questions shorter than this
- **Meta Instructions**: Context about the agent's role (Default Updater only)
- **Topic Shift Threshold**: Similarity threshold for detecting topic changes (0-1)
- **Max Tokens**: Maximum tokens for generated instructions

## When to Use

### Use Adaptive Instructions When:

✅ Your agent handles **multiple distinct topics** (e.g., customer support, technical issues, billing)

✅ You want **context-aware specialization** without creating multiple agents

✅ Conversations have **clear topic boundaries** that benefit from different instruction sets

✅ You need to **optimize response quality** for specific scenarios

### Don't Use When:

❌ Your agent has a **single, consistent purpose** throughout conversations

❌ **Performance is critical** and you want to minimize API calls

❌ You need **100% predictable behavior** (use static instructions instead)

❌ Your conversations have **frequent, rapid topic switches** (excessive updates)

## Performance Considerations

### Topic Shift Detection Cost

| Operation | Cost | Frequency |
|-----------|------|-----------|
| Embedding user message | Low | Every turn |
| Similarity computation | Negligible | Every turn |
| LLM instruction generation | Moderate | On topic shift only |

### Optimization Tips

1. **Tune the threshold**: Higher values (0.85+) = fewer updates, lower values (0.75-) = more sensitive
2. **Use fast embedding models**: `text-embedding-3-small` is recommended
3. **Set min message length**: Skip trivial messages
4. **Use rule-based updater**: When you can define scenarios upfront

## Best Practices

### 1. Start with Default Updater

Begin with the Default Instructions Updater to understand how your conversations flow:

```csharp
// Configure in AgentProfile inspector
Enable: true
Embedding Model: "text-embedding-3-small"
Generation Model: "gpt-4o-mini"
Topic Shift Threshold: 0.82
```

Monitor the logs to see when topic shifts occur and whether the generated instructions are appropriate.

### 2. Provide Clear Meta Instructions

Help the LLM understand your agent's purpose:

```
This agent provides customer support for our SaaS platform.
The agent should be professional, concise, and solution-focused.
Common topics include: billing questions, technical issues, feature requests.
```

### 3. Tune the Threshold

Experiment with different threshold values:

- **0.75-0.80**: Sensitive, updates frequently
- **0.82-0.85**: Balanced (recommended starting point)
- **0.86-0.90**: Conservative, only major topic shifts

### 4. Skip Short Messages

Prevent unnecessary updates for brief messages:

```csharp
Min Message Length: 10  // Skip "ok", "thanks", etc.
Min Question Length: 5  // Skip "why?", "how?", etc.
```

### 5. Monitor and Iterate

Enable logging to track instruction updates:

```csharp
// In your agent initialization
agent.LogLevel = LogLevel.Info;

// You'll see logs like:
// "Topic shift detected (similarity=0.75 < threshold=0.82). Regenerating..."
// "Instructions generated (245 chars)."
```

## Implementation Example

### Default Updater Setup

```csharp
using Glitch9.AIDevKit.Agents;
using UnityEngine;

public class AdaptiveAgent : AgentBehaviour
{
    protected override void Start()
    {
        base.Start();
        
        // Adaptive Instructions are configured in AgentProfile
        // and automatically applied when the capability is enabled
        
        // Optional: Access settings at runtime
        var settings = Profile.AdaptiveInstructionsSettings;
        if (settings != null)
        {
            Debug.Log($"Adaptive Instructions enabled with threshold: {settings.TopicShiftThreshold}");
        }
    }
}
```

### Monitor Topic Shifts

```csharp
public class MonitoredAgent : AgentBehaviour
{
    protected override void OnEnable()
    {
        base.OnEnable();
        
        // Subscribe to instruction changes
        if (Agent != null)
        {
            Agent.OnInstructionsUpdated += HandleInstructionsUpdated;
        }
    }
    
    private void HandleInstructionsUpdated(string newInstructions)
    {
        Debug.Log($"Instructions updated:\n{newInstructions}");
        
        // Optional: Store or analyze instruction changes
        // to understand conversation flow
    }
}
```

## Limitations

### Current Limitations

1. **Experimental Feature**: API may change in future versions
2. **Latency**: LLM generation adds response delay on topic shifts
3. **Cost**: Each topic shift requires embedding + (optionally) LLM generation
4. **Context Window**: Generated instructions don't see full conversation history

### Mitigations

- Use **rule-based updater** for predictable, low-latency scenarios
- **Cache aggressively** (Default Updater already does this)
- **Tune threshold** to reduce unnecessary updates
- Provide rich **meta instructions** to give the LLM context

## Debugging

### Enable Detailed Logging

```csharp
// In AgentBehaviour
protected override AgentOptions BuildAgentOptions()
{
    var options = base.BuildAgentOptions();
    options.LogLevel = LogLevel.Debug;
    return options;
}
```

### Check Update Frequency

```csharp
private int updateCount = 0;

private void HandleInstructionsUpdated(string newInstructions)
{
    updateCount++;
    Debug.Log($"Total updates: {updateCount}");
    
    // If updateCount is too high, increase threshold
    // If updateCount is too low, decrease threshold
}
```

### Validate Embedding Model

```csharp
// Test embedding generation
var testMessage = "Hello, I need help with billing";
var result = await testMessage
    .GENEmbedding()
    .SetModel("text-embedding-3-small")
    .ExecuteAsync();

if (result != null && result.Value.Length > 0)
{
    Debug.Log($"Embedding successful: {result.Value[0].Value.Length} dimensions");
}
else
{
    Debug.LogError("Embedding failed - check API key and model name");
}
```

## See Also

- [Default Instructions Updater](default-instructions-updater.md) - LLM-driven adaptive instructions
- [Custom Instructions Updater](custom-instructions-updater.md) - Rule-based adaptive instructions
- [Agent Profile](../components/agent-profile.md) - Agent configuration
- [How Agent Works](../overview/how-agent-works.md) - Understanding agent architecture
