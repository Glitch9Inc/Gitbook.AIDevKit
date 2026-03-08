---
icon: terminal
---

# Tool Call Managers

Tool Call Managers execute **local code** in response to AI-dispatched tool calls. The AI sends a structured call; the manager runs the corresponding logic and returns the result back to the agent.

All Tool Call Managers are discovered automatically by [Agent Behaviour](../agent-behaviour.md) via `GetComponentsInChildren` — no registration needed.

```csharp
public abstract class ToolCallManagerBase<TCall, TOutput> : MonoBehaviour
```

---

## Function Call Manager

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

## Shell Command Call Manager

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

## Computer Use Call Manager

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

## Custom Tool Call Manager

**Component:** `AI Dev Kit / Agent / Custom Tool Call Manager`

A base component for implementing completely custom AI-callable tools with arbitrary logic.

```csharp
public abstract class CustomToolCallManager : MonoBehaviour, ICustomToolExecutor
```

Subclass this component, override `ExecuteAsync`, and add your component to the Agent's `GameObject`. The agent discovers it automatically.

---

## Architecture

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
