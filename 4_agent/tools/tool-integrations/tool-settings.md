# Tool Settings

Tool Settings define how your agent handles tool calls at runtime, including timeout configurations and fallback behaviors for unhandled tool calls.

## Overview

The `AgentBehaviourToolSettings` class, configured in the `AgentBehaviour` component, provides fine-grained control over tool execution behavior. This is particularly useful when dealing with unexpected tool calls, manual interventions, or MCP integrations.

## Configuration

Tool Settings are configured in the AgentBehaviour component inspector and consist of three main properties:

### Unhandled Tool Call Behaviour

Defines the policy to apply when a tool call is received but the agent has no handler for it.

Available options:

#### LogAndIgnore (Default)
Logs that the tool call was unhandled. If the unhandled call prevents the conversation from progressing, the agent may attempt a fallback process to continue the dialogue flow.

```csharp
// Configured in inspector or code
var settings = new AgentBehaviourToolSettings(
    UnhandledToolCallBehaviour.LogAndIgnore,
    submitToolOutputTimeoutSeconds: 60,
    mcpApprovalTimeoutSeconds: 30
);
```

**Use when**: You want graceful degradation and the agent should continue operating even if some tools fail.

#### SubmitToolOutput
The agent transitions to a `WaitingForToolOutput` state, and you must manually call `Agent.SubmitToolOutput()` to provide the tool output before the run can continue.

```csharp
// Agent waits for manual submission
await agent.SubmitToolOutput(toolCallId, outputData);
```

**Use when**: You need manual intervention or human-in-the-loop approval for certain operations.

#### ReturnRefusal
Instead of raising an exception, the client directly creates and returns a `ResponseMessage` that contains error information, in place of the real model response.

**Use when**: You want to communicate tool failures back to the LLM so it can respond appropriately to the user.

#### Throw
Immediately throws an exception. Use this in strict or debugging scenarios where every tool call must be handled explicitly.

```csharp
// In strict mode, unhandled tools will throw
var settings = new AgentBehaviourToolSettings(
    UnhandledToolCallBehaviour.Throw,
    submitToolOutputTimeoutSeconds: 60,
    mcpApprovalTimeoutSeconds: 30
);
```

**Use when**: You're in development/debugging mode and want to catch missing tool handlers immediately.

### Submit Tool Output Timeout

Maximum time (in seconds) to wait for manual tool output submission when using `SubmitToolOutput` behaviour.

- **Range**: 0-300 seconds
- **Default**: 60 seconds

```csharp
// Configure 120 second timeout for manual submissions
var settings = new AgentBehaviourToolSettings(
    UnhandledToolCallBehaviour.SubmitToolOutput,
    submitToolOutputTimeoutSeconds: 120,
    mcpApprovalTimeoutSeconds: 30
);
```

### MCP Approval Timeout

Timeout (in seconds) for MCP (Model Context Protocol) tool approval prompts.

- **Range**: 1-300 seconds
- **Default**: Defined in `AIDevKitConfig.DefaultMcpApprovalTimeoutSeconds`

```csharp
// Configure 45 second timeout for MCP approvals
var settings = new AgentBehaviourToolSettings(
    UnhandledToolCallBehaviour.LogAndIgnore,
    submitToolOutputTimeoutSeconds: 60,
    mcpApprovalTimeoutSeconds: 45
);
```

## Usage in AgentBehaviour

The tool settings are automatically applied when building the `AgentOptions`:

```csharp
// In AgentBehaviour.cs (simplified)
protected virtual AgentOptions BuildAgentOptions()
{
    toolSettings ??= new AgentBehaviourToolSettings();
    
    return new AgentOptions
    {
        UnhandledToolCallBehaviour = toolSettings.UnhandledToolCallBehaviour,
        SubmitToolOutputTimeoutSeconds = toolSettings.SubmitToolOutputTimeoutSeconds,
        McpApprovalTimeoutSeconds = toolSettings.McpApprovalTimeoutSeconds,
        // ... other options
    };
}
```

## Best Practices

### Development vs Production

**Development**:
```csharp
// Strict mode - catch all unhandled tools
UnhandledToolCallBehaviour = UnhandledToolCallBehaviour.Throw
```

**Production**:
```csharp
// Graceful fallback - maintain user experience
UnhandledToolCallBehaviour = UnhandledToolCallBehaviour.LogAndIgnore
```

### Manual Intervention Workflows

When implementing human-in-the-loop patterns:

```csharp
// 1. Configure for manual submission
var settings = new AgentBehaviourToolSettings(
    UnhandledToolCallBehaviour.SubmitToolOutput,
    submitToolOutputTimeoutSeconds: 300, // 5 minute timeout
    mcpApprovalTimeoutSeconds: 30
);

// 2. Handle the waiting state in your code
if (agent.State == AgentState.WaitingForToolOutput)
{
    // Present UI to user for approval
    string output = await ShowApprovalDialog(agent.PendingToolCall);
    
    // Submit the result
    await agent.SubmitToolOutput(
        agent.PendingToolCall.Id, 
        output
    );
}
```

### MCP Integration

For MCP tools that require user confirmation:

```csharp
// Configure appropriate timeout for user interaction
var settings = new AgentBehaviourToolSettings(
    UnhandledToolCallBehaviour.LogAndIgnore,
    submitToolOutputTimeoutSeconds: 60,
    mcpApprovalTimeoutSeconds: 60 // Give user time to review
);
```

## Error Handling

When a tool call times out or fails:

```csharp
try
{
    await agent.ChatAsync("Use the custom tool");
}
catch (ToolExecutionException ex)
{
    Debug.LogError($"Tool execution failed: {ex.Message}");
    // Handle based on your UnhandledToolCallBehaviour setting
}
```

## See Also

- [Tool Routing](tool-routing.md) - Semantic tool selection
- [MCP Integration](../mcp-integration/README.md) - Model Context Protocol tools
- [Custom Tool](../custom-tool.md) - Creating custom tools
- [Approval Handlers](../mcp-integration/approval-handlers.md) - Implementing approval flows
