---
description: Set up your first Agent in Unity from scratch in a few minutes
icon: wand-magic-sparkles
---

# Creating Your First Agent

This guide walks you through creating a working Agent step by step. By the end you will have an Agent in your scene that can receive text messages and stream responses back to your code.

{% hint style="info" %}
Make sure you have already [set up your API key](../../2_getting-started/api-key-setup/README.md) before continuing.
{% endhint %}

---

## Step 1 - Create an AgentProfile

The `AgentProfile` defines your agent's **identity and personality**.

1. In the **Project** window, right-click a folder and choose:\
   **Create > AI Dev Kit > Agent > Agent Profile**
2. Name the asset (e.g. `MyAgentProfile`)
3. Fill in the Inspector fields:

| Field | Description |
|---|---|
| **Name** | The agent's display name (e.g. `Alex`) |
| **Instructions** | System prompt - tell the agent who it is and how to behave |
| **Conversation Starter** | *(Optional)* First message the agent sends when a conversation begins |
| **Traits** | *(Optional)* Personality toggles: Witty, Encouraging, Direct, etc. |

**Example instructions:**

```
You are Alex, a friendly game companion. Help the player with quests,
answer lore questions, and keep your responses concise.
```

---

## Step 2 - Create an AgentSettings

The `AgentSettings` defines the **technical configuration**.

1. Right-click and choose:\
   **Create > AI Dev Kit > Agent > Agent Settings**
2. Name the asset (e.g. `MyAgentSettings`)
3. In the Inspector, configure:

| Field | Description |
|---|---|
| **Chat API Type** | `ChatCompletions` for most use cases |
| **Model** | Select a model (e.g. `gpt-4o-mini`) |
| **Instructions** section | Assign your `AgentProfile` asset here |

Leave all other settings at their defaults for now.

---

## Step 3 - Add AgentBehaviour to Your Scene

1. Create an empty **GameObject** (e.g. name it `Agent`)
2. Click **Add Component** and search for **AgentBehaviour**
3. Assign your **AgentSettings** asset to the `Settings` field

You don't need to assign an audio recorder or player unless you want voice I/O.

---

## Step 4 - Send a Message from Code

Create a MonoBehaviour that holds a reference to `AgentBehaviour`:

```csharp
using Glitch9.AIDevKit.Agents;
using Cysharp.Threading.Tasks;
using UnityEngine;

public class ChatExample : MonoBehaviour
{
    [SerializeField] private AgentBehaviour agentBehaviour;

    private async void Start()
    {
        // Wait for the agent to finish initializing
        await agentBehaviour.Agent.InitializeAsync();

        // Send a message and await the full response
        var result = await agentBehaviour.Agent.SendAsync("Hello! Who are you?");

        // Print the response text
        Debug.Log(result.Value.GetText());
    }
}
```

Attach this script to any GameObject and wire up the `AgentBehaviour` reference in the Inspector.

---

## Step 5 - Handle Streaming Responses *(optional)*

For a better user experience, show text as it streams rather than waiting for the complete response:

```csharp
using Glitch9.AIDevKit.Agents;
using Glitch9.AIDevKit;
using Cysharp.Threading.Tasks;
using UnityEngine;
using UnityEngine.UI;

public class StreamingChatExample : MonoBehaviour
{
    [SerializeField] private AgentBehaviour agentBehaviour;
    [SerializeField] private Text responseText;

    private void OnEnable()
    {
        agentBehaviour.Agent.RegisterCallback<Delta<ITextChunk>>(OnTextDelta);
    }

    private void OnDisable()
    {
        agentBehaviour.Agent.UnregisterCallback<Delta<ITextChunk>>(OnTextDelta);
    }

    private void OnTextDelta(Delta<ITextChunk> delta)
    {
        // Append each token chunk to your UI text
        responseText.text += delta.Value.Text;
    }

    public async void OnSendButtonClicked(string userMessage)
    {
        responseText.text = "";
        await agentBehaviour.Agent.SendAsync(userMessage);
    }
}
```

{% hint style="info" %}
Streaming must be enabled in **AgentSettings > Model Parameters > Stream** for `OnTextDelta` events to fire.
{% endhint %}

---

## AutoInit vs Manual Init

`AgentBehaviour` exposes an **AutoInit** option in the Inspector:

| Mode | When to use |
|---|---|
| **AutoInit = On** *(default)* | Agent initializes when the scene starts. Safe for most projects. |
| **AutoInit = Off** | You call `Agent.InitializeAsync()` yourself. Useful when you need to configure dependencies at runtime before the agent starts. |

---

## What's Next

* Learn what happens internally when a message is sent -> [How Agent Works](how-agent-works.md)
* Persist conversation history across sessions -> [Saving & Loading](../conversations/saving-and-loading.md)
* Add function calling (tools) -> Tools section
* Enable voice input/output -> Audio section
