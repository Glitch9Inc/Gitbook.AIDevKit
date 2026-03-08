---
icon: wrench
---

# Tool Managers

Tool Managers extend an [Agent Behaviour](agent-behaviour.md) with specific AI tool capabilities. Each manager is a `MonoBehaviour` that is **auto-discovered** by `AgentBehaviour` at runtime via `GetComponentsInChildren` — no manual wiring required.

There are two distinct families:

| Family | Base Class | Purpose |
|---|---|---|
| **Tool Manager** | `ToolManagerBase<TOutput, TSettings>` | Registers built-in provider tools (web search, file search, MCP, image generation, code interpreter) and handles their outputs. |
| **Tool Call Manager** | `ToolCallManagerBase<TCall, TOutput>` | Executes local tool calls dispatched by the AI (function calls, shell commands, computer use, custom). |

---

## Tool Managers

### Web Search Manager

**Component:** `AI Dev Kit / Agent / Web Search Manager`

Registers the Web Search tool and handles search results.

```csharp
public sealed class WebSearchManager : ToolManagerBase<WebSearchOutput, WebSearchSettings>
```

**Settings (`WebSearchSettings`):** Configure search parameters such as context window size and domain restrictions.

**Event:**

| Event | Signature | Description |
|---|---|---|
| `onWebSearchOutput` | `UnityEvent<WebSearchOutput>` | Invoked when the Web Search tool returns results. |

---

### File Search Manager

**Component:** `AI Dev Kit / Agent / File Search Manager`

Registers the File Search tool (searches uploaded files in a vector store) and handles results.

```csharp
public sealed class FileSearchManager : ToolManagerBase<FileSearchOutput, FileSearchSettings>
```

**Settings (`FileSearchSettings`):** Configure the vector store ID and max result count.

**Event:**

| Event | Signature | Description |
|---|---|---|
| `onFileSearchOutput` | `UnityEvent<FileSearchOutput>` | Invoked when the File Search tool returns results. |

---

### MCP Manager

**Component:** `AI Dev Kit / Agent / MCP Manager`

Registers one or more MCP (Model Context Protocol) servers and handles their tool call results. To connect to multiple servers, add one `McpManager` per server — each manager independently routes output by server label.

```csharp
public sealed class McpManager : ToolManagerBase<McpOutput, McpManagerSettings>
```

**Settings (`McpManagerSettings`):**

| Field | Type | Description |
|---|---|---|
| `servers` | `McpToolDefinition[]` | List of MCP server definitions (URL, label, headers, etc.). |

**Fields:**

| Field | Type | Description |
|---|---|---|
| `approvalTimeoutSeconds` | `int` | Seconds to wait for safety-check approval before timing out. |

**Events:**

| Event | Signature | Description |
|---|---|---|
| `onMcpOutput` | `UnityEvent<McpOutput>` | Invoked when an MCP tool call result arrives. |
| `onMcpListToolsOutput` | `UnityEvent<McpListToolsCallOutput>` | Invoked when the server's tool list is received. |

---

### Image Generation Manager

**Component:** `AI Dev Kit / Agent / Image Generation Manager`

Registers the Image Generation tool and handles both streaming image frames and final outputs.

```csharp
public sealed class ImageGenerationManager : ToolManagerBase<ImageGenerationOutput, ImageGenerationRequestSettings>
```

**Settings (`ImageGenerationRequestSettings`):** Configure size, quality, model, style, and other DALL·E / image model parameters.

**Event:**

| Event | Signature | Description |
|---|---|---|
| `onGeneratedImage` | `UnityStreamEvent<Texture2D>` | Fires as streaming frames arrive (`InvokeDelta`) and on completion (`InvokeComplete`). |

> **Note:** `UnityStreamEvent<T>` provides both delta (incremental) and complete callbacks in a single event field. Subscribe with `onGeneratedImage.AddListenerDelta(...)` or `onGeneratedImage.AddListenerComplete(...)`.

---

### Code Interpreter Manager

**Component:** `AI Dev Kit / Agent / Code Interpreter Manager`

Handles streaming code output, log output, and image output from the Code Interpreter tool.

```csharp
public sealed class CodeInterpreterManager : ToolManagerBase<CodeInterpreterOutput, CodeInterpreterSettings>
```

**Settings (`CodeInterpreterSettings`):** Configure file IDs available to the interpreter.

**Events:**

| Event | Signature | Description |
|---|---|---|
| `onCodeInterpreterCode` | `UnityStreamEvent<string>` | Fires incrementally as code text is streamed, then fires complete with the full code. |
| `onCodeInterpreterOutputLogs` | `UnityEvent<string>` | Fires when the interpreter emits text log output. |
| `onCodeInterpreterOutputImage` | `UnityEvent<Texture2D>` | Fires when the interpreter produces an image (downloaded asynchronously from URL). |

---

## Tool Call Managers

Tool Call Managers execute **local code** in response to AI-dispatched tool calls. The AI sends a structured call; the manager runs the corresponding logic and returns the result.

### Function Call Manager

**Component:** `AI Dev Kit / Agent / Function Call Manager`

Binds C# methods to AI-callable functions. Supports `void`, `UniTask`, and `UniTask<T>` return types, `CancellationToken` parameters, and full JSON argument binding.

```csharp
public class FunctionCallManager : ToolCallManagerBase<FunctionCall, FunctionOutput>, IFunctionExecutor
```

**Fields:**

| Field | Type | Description |
|---|---|---|
| `functions` | `List<FunctionDescriptor>` | Registered C# method bindings. Configure via the Inspector. |

**Add a function binding** in the Inspector under **Add Function**, then select the target component and method name. The method signature is inspected at runtime to bind JSON arguments automatically.

---

### Shell Command Call Manager

**Component:** `AI Dev Kit / Agent / Shell Command Call Manager`

Executes local shell commands on behalf of the AI. Only whitelisted commands (registered via the **Shell Command Catalog**) can be executed.

```csharp
public class ShellCommandCallManager : ToolCallManagerBase<LocalShellCall, LocalShellOutput>, ILocalShellExecutor
```

**Fields:**

| Field | Type | Description |
|---|---|---|
| `registeredCommandKeys` | `string[]` | Whitelist of command keys the AI is permitted to execute. |
| `logOutputs` | `bool` | Log stdout/stderr to the Unity console. |

> **Security note:** Always restrict `registeredCommandKeys` to the minimum set of commands your application requires.

---

### Computer Use Call Manager

**Component:** `AI Dev Kit / Agent / Computer Use Call Manager`

Enables the AI to control the device — simulating mouse clicks, keyboard input, drag operations, scrolling, and screenshots.

```csharp
public class ComputerUseCallManager : ToolCallManagerBase<ComputerUseCall, ComputerUseOutput>, IComputerUseExecutor
```

**Fields:**

| Field | Type | Description |
|---|---|---|
| `waitTimeInSeconds` | `float` | Delay between consecutive computer use actions. |
| `onInputEventReceived` | `UnityEvent<ComputerUseAction>` | Fires for each input action so you can implement custom action handling. |

Implement `IComputerUseActionHandler` to provide the actual platform-specific input simulation.

---

### Custom Tool Call Manager

**Component:** `AI Dev Kit / Agent / Custom Tool Call Manager`

A base component for implementing completely custom AI-callable tools with arbitrary logic.

```csharp
public abstract class CustomToolCallManager : MonoBehaviour, ICustomToolExecutor
```

Subclass this component, override `ExecuteAsync`, and add your component to the Agent's `GameObject`. The agent discovers it automatically.

---

## Architecture Summary

```
AgentBehaviour (auto-discovers all managers on Start)
├── ToolManagerBase children          → Register provider tools + handle tool outputs
│   ├── WebSearchManager
│   ├── FileSearchManager
│   ├── McpManager
│   ├── ImageGenerationManager
│   └── CodeInterpreterManager
└── ToolCallManagerBase / IExecutor children   → Execute local tool calls
    ├── FunctionCallManager
    ├── ShellCommandCallManager
    ├── ComputerUseCallManager
    └── CustomToolCallManager (user-defined)
```

Both families are discovered via `GetComponentsInChildren` — no registration calls needed.
