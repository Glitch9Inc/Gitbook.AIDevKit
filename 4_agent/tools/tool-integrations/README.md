# Tool Integrations

Tool Integrations provide advanced configurations for managing how your agent handles tool calls and routes tools to the LLM.

## Overview

AI Dev Kit's Tool Integration system offers two primary features:

- **Tool Settings**: Defines how the agent behaves when receiving tool calls, including timeout configurations and fallback behaviors
- **Tool Routing**: Uses semantic embedding-based routing to intelligently select the most relevant tools for each user message

## Key Features

### Tool Settings

Configure agent behavior when handling tool calls:

- **Unhandled Tool Call Behavior**: Define fallback strategies when a tool call has no handler
- **Submit Tool Output Timeout**: Set maximum wait time for manual tool output submission
- **MCP Approval Timeout**: Configure timeout for MCP (Model Context Protocol) tool approval

### Tool Routing

Optimize token usage and improve response quality:

- **Semantic Tool Selection**: Uses embedding-based similarity to select the most relevant tools
- **Dynamic Top-K Filtering**: Automatically reduces the tool list sent to the LLM
- **Automatic Index Management**: Builds and maintains an in-memory vector index of tool descriptions

## Components

### AgentBehaviourToolSettings

Managed in `AgentBehaviour` component, this defines the runtime behavior for tool execution.

### AgentToolRoutingSettings

Managed in `AgentProfile` component, this configures the embedding model used for semantic tool routing.

## Use Cases

**Tool Settings** is useful when:
- You need to handle unexpected tool calls gracefully
- You want to implement manual intervention for specific tools
- You need to configure timeout behavior for MCP integrations

**Tool Routing** is useful when:
- You have a large number of tools (>12) registered
- You want to reduce prompt token count by sending only relevant tools
- You need context-aware tool selection based on user messages

## Next Steps

- Learn about [Tool Settings](tool-settings.md)
- Learn about [Tool Routing](tool-routing.md)
- Explore [MCP Integration](../mcp-integration/README.md)
- Check out [Custom Tools](../custom-tool.md)
