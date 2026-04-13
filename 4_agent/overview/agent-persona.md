# Agent Persona

## What is Agent Persona?

Agent Persona is a reusable configuration asset that defines the core identity, personality, and behavioral instructions for your AI agent. It separates the "who" (persona) from the "how" (technical settings like model, temperature, etc.).

Think of Persona as the agent's character sheet—it contains all the information that makes your agent unique and consistent across conversations.

---

## Why Use Persona?

### Separation of Concerns

- **Persona**: Defines identity, tone, role, instructions
- **Agent Profile**: Defines technical settings (model, parameters, capabilities)
- **Agent Behaviour**: Runs the agent in your scene with runtime state

This separation allows you to:
- Reuse the same persona across multiple agent profiles
- Update agent personality without touching technical settings
- Maintain consistency across different deployment scenarios

### Consistency

Without a Persona, instructions scattered across different places can lead to:
- Inconsistent agent behavior
- Difficult maintenance
- Redundant configuration

With Persona, your agent's identity is centralized and version-controlled.

---

## Creating a Persona

### Step 1: Create the Asset

**In Unity Editor:**

1. Right-click in the Project window
2. Navigate to: **Create > AI DevKit > Agent Persona (Instruct)**
3. Name your persona (e.g., "TutorPersona", "CustomerSupportAgent")

### Step 2: Configure Core Properties

Open the created Persona asset and configure:

#### Agent Name
```
The name your agent will use to identify itself.
Example: "Alex", "Support Bot", "Professor Lee"
```

#### User Name (Optional)
```
The name the agent should use to address the user.
Example: "Student", "Player", "Customer"
Leave blank if not needed.
```

#### Description
```
A brief summary of the agent's purpose.
Example: "A friendly math tutor specializing in algebra and calculus"
```

#### Instructions
```
The core system prompt that defines the agent's behavior, tone, and capabilities.
This is the most important field.
```

#### Starting Message (Optional)
```
The first message the agent sends when a conversation begins.
Example: "Hello! I'm Alex, your math tutor. What would you like to learn today?"
```

---

## Persona Types

AI Dev Kit provides two persona types:

### 1. Instruct Persona (Recommended)

**Create > AI DevKit > Agent Persona (Instruct)**

Best for most use cases. Provides a clean, straightforward configuration:
- Agent Name
- User Name
- Description
- Instructions (system prompt)
- Starting Message

Use this when:
- Building conversational agents
- Creating assistants or tutors
- Standard chatbot applications

### 2. NPC Persona (Game Characters)

**Create > AI DevKit > Agent Persona (NPC)**

Specialized for game characters with additional fields:
- Character stats (health, mana, etc.)
- Inventory and equipment
- Relationship tracking with other NPCs
- Quest and objective management

Use this when:
- Building RPG or adventure game characters
- Need state management beyond conversation
- Require game-specific metadata

---

## Writing Effective Instructions

Instructions are the core of your Persona. Here's how to write them effectively:

### Structure Template

```
# Role
You are [name], a [role description].

# Personality
- [trait 1]
- [trait 2]
- [trait 3]

# Expertise
You specialize in:
- [area 1]
- [area 2]
- [area 3]

# Tone & Style
- [communication style]
- [language preferences]
- [formality level]

# Constraints & Rules
- [what to do]
- [what to avoid]
- [boundaries]

# Examples (Optional)
User: [example question]
You: [example response]
```

### Example: Math Tutor

```markdown
# Role
You are Alex, an experienced and patient math tutor with 10 years of teaching experience.

# Personality
- Patient and encouraging
- Breaks down complex concepts into simple steps
- Uses real-world examples when possible
- Celebrates student progress

# Expertise
You specialize in:
- Algebra (grades 7-12)
- Calculus (basic to advanced)
- Problem-solving strategies
- Test preparation

# Tone & Style
- Use clear, simple language
- Ask guiding questions instead of giving direct answers
- Provide step-by-step explanations
- Use encouraging phrases like "Great work!" or "You're on the right track!"

# Constraints & Rules
- Never solve problems entirely for the student
- Always verify understanding before moving on
- If a topic is outside your expertise (geometry, statistics), politely redirect
- Keep responses concise (2-3 sentences when possible)

# Response Format
When solving problems:
1. Acknowledge the student's question
2. Guide them with hints
3. Break down steps
4. Ask verification questions
```

### Best Practices

**DO:**
- ✅ Be specific about role and expertise
- ✅ Define clear boundaries (what agent can/cannot do)
- ✅ Include personality traits
- ✅ Specify response format expectations
- ✅ Use examples when helpful

**DON'T:**
- ❌ Write vague instructions ("Be helpful")
- ❌ Include technical parameters (temperature, tokens) - those go in Agent Profile
- ❌ Repeat yourself unnecessarily
- ❌ Make instructions overly long (aim for 1-2 pages max)
- ❌ Use contradictory rules

---

## Using Persona with Agent Profile

### Assignment

Once you've created a Persona:

1. Open your **Agent Profile** asset
2. In the **General** section, find the **Persona** field
3. Drag your Persona asset into this field (or click the circle to select)

### Persona + Profile Combination

```
Persona (WHO)                  Agent Profile (HOW)
├─ Agent Name                  ├─ Model (gpt-4o, claude-3, etc.)
├─ Instructions                ├─ Temperature
├─ Tone & Personality          ├─ Max Tokens
└─ Starting Message            ├─ Capabilities
                               └─ Technical Settings
```

The Agent Profile reads from the Persona at runtime and combines it with technical settings to create the complete agent configuration.

---

## Reusing Personas

One of the key benefits of Persona is reusability:

### Same Persona, Different Profiles

```
TutorPersona
    ├─ TutorProfile_Fast (gpt-3.5-turbo, low cost)
    ├─ TutorProfile_Quality (gpt-4o, high quality)
    └─ TutorProfile_Local (ollama, self-hosted)
```

All three profiles share the same personality and instructions, but use different models and settings.

### Swapping Personas

You can easily swap personas at runtime or in the inspector:

```csharp
// Change persona at runtime
agentBehaviour.Profile.Persona = friendlyPersona;

// Or swap the entire profile
agentBehaviour.Profile = customerSupportProfile;
```

---

## Tips & Tricks

### Context Injection

You can dynamically inject context into instructions:

```csharp
persona.Instructions = baseInstructions + $"\n\nCurrent game state: {gameState}";
```

### Persona Variants

Create variants for different moods or scenarios:

```
CustomerAgent_Friendly
CustomerAgent_Professional
CustomerAgent_Technical
```

### Version Control

Since Personas are assets, they're tracked by version control. Use meaningful commit messages:

```
"Updated TutorPersona: Added calculus expertise"
"Created DebugAssistant persona for dev mode"
```

### Testing

Test your persona with various prompts before deployment:
- Edge cases
- Boundary violations
- Unclear requests
- Multi-turn conversations

---

## Common Patterns

### Information Gathering Agent

```markdown
You are an intake specialist. Your job is to gather information before 
providing recommendations.

Always:
1. Ask clarifying questions
2. Summarize what you've learned
3. Confirm understanding before proceeding
```

### Domain Expert

```markdown
You are Dr. Chen, a marine biologist with 20 years of research experience.

Expertise:
- Coral reef ecosystems
- Ocean conservation
- Marine biodiversity

When explaining:
- Use scientific accuracy
- Include fascinating facts
- Make complex topics accessible
```

### Game NPC

```markdown
You are Gorlock, a grumpy but kind-hearted blacksmith in the village of Oakvale.

Personality:
- Gruff exterior, warm interior
- Dislikes small talk
- Respects hard workers
- Has a dry sense of humor

Speech pattern:
- Short, direct sentences
- Occasional grumbling
- Uses blacksmith metaphors
```

---

## Troubleshooting

### Agent Not Following Instructions

**Problem:** Agent ignores persona instructions
**Solution:** 
- Check that Persona is assigned to Agent Profile
- Verify instructions aren't contradicted by model behavior
- Try more explicit, directive language
- Consider using a more capable model

### Instructions Too Long

**Problem:** Token limit exceeded
**Solution:**
- Condense instructions to core essentials
- Remove redundant statements
- Use bullet points instead of paragraphs
- Split complex personas into focused variants

### Inconsistent Behavior

**Problem:** Agent behaves differently across conversations
**Solution:**
- Review instructions for ambiguity
- Add explicit constraints
- Test with multiple conversation scenarios
- Consider lowering temperature in Agent Profile

---

## Next Steps

- [Creating Your First Agent](creating-your-first-agent.md)
- [Agent Profile](../../README.md)
- [How Agent Works](how-agent-works.md)
- [Available Capabilities](../../../1_introduction/package-tiers.md)
