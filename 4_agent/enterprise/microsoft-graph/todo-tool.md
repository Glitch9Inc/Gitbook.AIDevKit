# Todo Tool

Create and manage tasks through natural language.

## Overview

The Todo Tool enables AI agents to manage Microsoft To Do - create tasks, set reminders, and organize lists.

## Setup

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;

// 1. Create and initialize the tool
var todoTool = new TodoToolManager();
await todoTool.InitializeAsync();

// 2. Add to your agent
agent.AddTool(todoTool);
```

## Usage Examples

```csharp
// Create tasks
await agent.SendAsync("Add a task to review the budget report by Friday");
await agent.SendAsync("Remind me to call John tomorrow at 2 PM");
await agent.SendAsync("Create a high priority task for the presentation");

// Check tasks
await agent.SendAsync("What tasks do I have due this week?");
await agent.SendAsync("Show me my important tasks");
await agent.SendAsync("List all unfinished tasks");

// Update tasks
await agent.SendAsync("Mark the design review task as complete");
await agent.SendAsync("Change the deadline for budget review to next Monday");

// Organize
await agent.SendAsync("Create a list for Project Alpha tasks");
await agent.SendAsync("Move this task to the Work list");
```

## Configuration

### Permissions Required

In Azure AD, grant:
- `Tasks.ReadWrite` - Full task access

### Task Lists

Tasks are organized into lists. The default list is used unless specified:

```csharp
// Create tasks in specific lists
await agent.SendAsync("Add a task to my Personal list");
await agent.SendAsync("Create a Work task for the meeting");
```

## Best Practices

1. **Due Dates**: Always set deadlines for time-sensitive tasks
2. **Priorities**: Mark important tasks as high priority
3. **Reminders**: Use reminders for critical deadlines
4. **Lists**: Organize tasks by project or category
5. **Details**: Add context in task descriptions

## Troubleshooting

### Task Not Created

**Problem**: Agent confirms but task doesn't appear

**Solutions**:
- Check Tasks.ReadWrite permission
- Verify To Do app is synced
- Refresh To Do app
- Check default list configuration

### Wrong Due Date

**Problem**: Task has incorrect deadline

**Solutions**:
- Be specific with dates ("next Friday" vs "this Friday")
- Include time zone if needed
- Verify date interpretation in agent response

### Task Not Found

**Problem**: Agent can't find task to update

**Solutions**:
- Be specific about task name
- Check if task was completed/deleted
- Verify correct list is being searched

## Related Topics

- [Authentication & Setup](authentication.md)
- [Microsoft Graph](README.md)
- [Calendar Tool](calendar-tool.md)
