---
icon: wrench
---

# Tool Managers

Tool Managers extend an [Agent Behaviour](agent-behaviour.md) with specific AI tool capabilities. Each manager is a `MonoBehaviour` that is **auto-discovered** by `AgentBehaviour` at runtime via `GetComponentsInChildren` — no manual wiring required.

```csharp
public abstract class ToolManagerBase<TOutput, TSettings> : MonoBehaviour
```

For AI-dispatched local tool calls (function calls, shell commands, computer use), see [Tool Call Managers](tool-call-managers.md).

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
