---
icon: id-card
---

# Agent Profile

`AgentProfile` is a reusable ScriptableObject that defines all configuration settings for an AI agent—personality, model, capabilities, and behavior parameters.

## Component Menu

```
Create > AI Dev Kit > Agent Profile
```

## Overview

Agent Profile centralizes agent configuration in a single asset, separating:
- **WHAT** the agent is (personality, role, capabilities) 
- **HOW** it behaves (model, temperature, token limits)
- **WHERE** it runs ([Agent Behaviour](agent-behaviour.md) MonoBehaviour)

This separation allows you to:
- Reuse the same profile across multiple scenes or GameObjects
- Swap profiles at runtime to change agent personality
- Maintain consistent agent behavior across deployments
- Version control agent configurations independently

## Architecture

```
AgentProfile (ScriptableObject)
    ├─ Persona (WHO)
    ├─ Model + API Settings (HOW)
    ├─ Capabilities (WHAT CAN DO)
    └─ Text/Tool/Audio Settings (BEHAVIOR)
        ↓
AgentBehaviour (MonoBehaviour)
    └─ Runtime execution in scene
```

### Profile vs Persona vs Behaviour

| Component | Type | Responsibility | Example |
|---|---|---|---|
| [**Agent Persona**](agent-persona.md) | ScriptableObject | Identity, instructions, tone | "Friendly math tutor" |
| **Agent Profile** | ScriptableObject | Technical settings, capabilities | "Use GPT-4o, streaming enabled" |
| [**Agent Behaviour**](agent-behaviour.md) | MonoBehaviour | Scene runtime, conversation state | Active conversation, current message |

---

## Creating a Profile

### Step 1: Create the Asset

1. Right-click in the Project window
2. Navigate to: **Create > AI Dev Kit > Agent Profile**
3. Name your profile (e.g., "TutorProfile", "CustomerSupportAgent")

### Step 2: Configure Settings

Open the created Profile asset to access the following sections:

---

## Inspector Sections

### General

Core identity and model selection.

| Field | Description |
|---|---|---|
| **Persona** | Assigns an [Agent Persona](agent-persona.md) asset that defines personality and instructions |
| **Model** | AI model to use (gpt-4o, claude-3-5-sonnet, etc.) |

**Example:**
```
Persona: TutorPersona (defines "You are a patient math tutor")
Model: gpt-4o
```

### Capabilities

Feature flags that enable/disable agent abilities. Only enabled capabilities appear in the inspector.

| Capability | Description |
|---|---|
| **Chat** | Text-based conversation (always enabled) |
| **Vision** | Image input processing |
| **Reasoning** | Extended thinking mode (o1, o3 models) |
| **Tools** | Function calling and tool execution |
| **Audio Input** | Speech-to-text transcription |
| **Audio Output** | Text-to-speech generation |
| **Adaptive Instructions** | Dynamic instruction generation based on conversation context (Experimental) |

**Usage:**
- Unchecked capabilities are hidden from the inspector
- Checked capabilities expand to show their settings
- Reduces inspector clutter to only relevant settings

### Text Output Settings

Controls text generation behavior.

#### Basic Settings

| Field | Default | Description |
|---|---|---|
| **Temperature** | 1.0 | Randomness (0 = deterministic, 2 = creative) |
| **Top P** | 1.0 | Nucleus sampling threshold |
| **Max Tokens** | 2048 | Maximum response length |
| **Frequency Penalty** | 0 | Penalize token repetition |
| **Presence Penalty** | 0 | Penalize topic repetition |

#### Reasoning Options

*Enabled when **Reasoning** capability is checked.*

| Field | Default | Description |
|---|---|---|
| **Effort** | Medium | Thinking intensity (Low/Medium/High) |
| **Include Summary** | True | Include reasoning summary in response |

**Model Support:**
- Reasoning is only available for OpenAI o1/o3 series models
- Other models will ignore reasoning settings

### Tool Settings

*Enabled when **Tools** capability is checked.*

| Field | Description |
|---|---|
| **Tool Choice** | Force tool usage (`None`, `Auto`, `Required`, or specific tool) |
| **Parallel Tool Calls** | Allow multiple tools to execute simultaneously |
| **Max Tool Roundtrips** | Maximum tool execution cycles before stopping (prevents infinite loops) |

**Best Practices:**
- Use `Tool Choice: Auto` for most cases
- Set `Max Tool Roundtrips: 3-5` to prevent runaway tool execution
- Enable `Parallel Tool Calls` only if tools are independent

### Audio Settings

*Enabled when **Audio Input** or **Audio Output** capabilities are checked.*

#### Audio Input (Speech-to-Text)

| Field | Default | Description |
|---|---|---|
| **Transcription Model** | whisper-1 | Model for speech recognition |
| **Language** | Auto-detect | Target language for transcription |

#### Audio Output (Text-to-Speech)

| Field | Default | Description |
|---|---|---|
| **Voice** | alloy | Voice profile for speech synthesis |
| **Speed** | 1.0 | Playback speed (0.25x - 4.0x) |
| **Audio Format** | MP3 | Output format (MP3, Opus, AAC, FLAC, WAV, PCM) |

### Adaptive Instructions (Experimental)

*Enabled when **Adaptive Instructions** capability is checked.*

Dynamically generates additional instructions based on conversation topics using RAG (Retrieval-Augmented Generation).

| Field | Description |
|---|---|
| **Embedding Model** | Model for topic extraction (e.g., text-embedding-3-small) |
| **Generation Model** | Model for instruction generation (e.g., gpt-4o-mini) |
| **Score Threshold** | Minimum similarity score to trigger instruction update |
| **Meta Instructions** | Guidelines for generating dynamic instructions |

**Use Case:**
- Expert systems that adapt to user expertise level
- Context-aware customer support bots
- Educational tutors that adjust to student progress

---

## Reusability Patterns

### Pattern 1: Environment-Specific Profiles

```
TutorProfile_Development (gpt-3.5-turbo, fast iteration)
TutorProfile_Staging (gpt-4o-mini, cost-effective testing)
TutorProfile_Production (gpt-4o, high quality)
```

All profiles share the same Persona but use different models/settings.

### Pattern 2: Capability Variants

```
BasicAgent (Chat only)
VisionAgent (Chat + Vision)
FullAgent (Chat + Vision + Tools + Audio)
```

Progressive feature enablement based on user tier or use case.

### Pattern 3: Personality Variants

```
FriendlySupport (Persona: Warm, Temperature: 1.2)
ProfessionalSupport (Persona: Formal, Temperature: 0.7)
TechnicalSupport (Persona: Expert, Temperature: 0.5)
```

Same base capabilities, different personalities and creativity levels.

### Pattern 4: Runtime Swapping

```csharp
// Swap profile at runtime
agentBehaviour.Profile = tutorProfile;

// Or just swap persona within the same profile
agentBehaviour.Profile.Persona = friendlyPersona;
```

Dynamically change agent behavior without recreating components.

---

## Code Integration

### Reading Profile Settings

```csharp
// Access profile through AgentBehaviour
AgentProfile profile = agentBehaviour.Profile;

// Check capabilities
if (profile.Capabilities.HasFlag(AgentCapabilities.Vision))
{
    // Agent can process images
}

// Get settings
float temperature = profile.TextOutputSettings.Temperature;
string model = profile.Model;
```

### Modifying at Runtime

```csharp
// Change model
agentBehaviour.Model = "gpt-4o-mini";

// Adjust temperature
agentBehaviour.Temperature = 0.8f;

// Note: These changes are runtime-only and don't modify the asset
```

### Creating Profiles Programmatically

```csharp
var profile = ScriptableObject.CreateInstance<AgentProfile>();
profile.Model = "gpt-4o";
profile.Capabilities = AgentCapabilities.Chat | AgentCapabilities.Tools;
profile.TextOutputSettings.Temperature = 0.7f;

// Save as asset (Editor only)
#if UNITY_EDITOR
AssetDatabase.CreateAsset(profile, "Assets/Profiles/DynamicProfile.asset");
AssetDatabase.SaveAssets();
#endif
```

---

## Best Practices

### Design

- ✅ **One Profile Per Agent Type**: Create separate profiles for different agent roles
- ✅ **Shared Personas**: Reuse Persona assets across profiles when personality is consistent
- ✅ **Capability Flags**: Only enable capabilities you actually use
- ✅ **Descriptive Names**: Use clear naming (e.g., `CustomerSupport_Production_GPT4o`)

### Performance

- ✅ **Token Limits**: Set appropriate `Max Tokens` to prevent excessive costs
- ✅ **Tool Roundtrips**: Limit `Max Tool Roundtrips` to prevent infinite loops
- ✅ **Model Selection**: Use cheaper models (gpt-4o-mini) for development
- ✅ **Parallel Tools**: Only enable if tools are truly independent

### Security

- ✅ **Tool Validation**: Always validate tool inputs in your Tool Managers
- ✅ **Token Budgets**: Set conversation-level token limits in Memory Settings
- ✅ **Sensitive Data**: Don't hardcode API keys or secrets in profiles

### Maintenance

- ✅ **Version Control**: Commit profiles as text assets (YAML serialization)
- ✅ **Documentation**: Add comments in the Persona instructions field
- ✅ **Testing**: Create test profiles with lower costs/limits
- ✅ **Migration**: Use `[FormerlySerializedAs]` when renaming fields

---

## Common Issues

### Profile Not Applied

**Problem:** Changes to profile don't take effect  
**Solution:**
- Ensure profile is assigned to `AgentBehaviour.Profile`
- Check that Agent is re-initialized after profile changes
- Runtime modifications don't persist to asset—save manually if needed

### Capabilities Grayed Out

**Problem:** Settings sections are disabled in inspector  
**Solution:**
- Enable the corresponding capability flag (e.g., check "Tools" to access Tool Settings)
- Some capabilities require specific models (e.g., Reasoning requires o1/o3)

### Model Not Found

**Problem:** Selected model returns 404 error  
**Solution:**
- Verify model name matches provider's API (e.g., `gpt-4o` not `gpt4o`)
- Check that your API key has access to the model
- Some models require special access (e.g., o1-preview)

### Tool Settings Ignored

**Problem:** Tools aren't being called  
**Solution:**
- Ensure `Tools` capability is enabled
- Check that Tool Managers are attached to Agent or child objects
- Verify `Tool Choice` is set to `Auto` or `Required`
- Review tool registration in [Tool Managers](tool-managers.md)

---

## Next Steps

- [Agent Persona](agent-persona.md) - Define agent personality and instructions
- [Agent Behaviour](agent-behaviour.md) - Attach profile to scene GameObject
- [Tool Managers](tool-managers.md) - Enable function calling capabilities
- [Creating Your First Agent](../overview/creating-your-first-agent.md) - Complete setup guide
