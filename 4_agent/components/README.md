---
icon: robot
---

# Components

Agent Components are `MonoBehaviour` scripts that bring AI agent capabilities into Unity scenes. Attach them to a `GameObject` and configure via the Inspector — no scripting required for basic setups.

| Component                                                                              | Description                                                                                                               |
| -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| [Agent Behaviour](../../AIDevKit/Unity.AIDevKit/5_components/agent-behaviour.md)       | The central AI component. Wraps the core `Agent` runtime and manages conversations, streaming, and tool routing.          |
| [Tool Managers](../../AIDevKit/Unity.AIDevKit/5_components/tool-managers.md)           | Register provider-side tools (web search, file search, MCP, image generation, code interpreter) and handle their outputs. |
| [Tool Call Managers](../../AIDevKit/Unity.AIDevKit/5_components/tool-call-managers.md) | Execute local C# logic in response to AI-dispatched tool calls (function calls, shell commands, computer use, custom).    |

## How They Work Together

```
AgentBehaviour
├── Manages the Agent runtime (settings, conversations, streaming)
├── Auto-discovers Tool Managers via GetComponentsInChildren
│   └── Registers provider tools and routes tool outputs
└── Auto-discovers Tool Call Managers via GetComponentsInChildren
    └── Executes local code for AI-dispatched tool calls
```

All managers are discovered automatically at startup — simply add the component to the same `GameObject` or any child.
