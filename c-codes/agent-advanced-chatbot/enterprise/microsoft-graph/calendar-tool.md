# Calendar Tool

Manage calendars and schedule meetings through natural language.

## Overview

The Calendar Tool enables AI agents to manage Microsoft 365 calendars - schedule meetings, check availability, and query events.

## Setup

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;

// 1. Create and initialize the tool
var calendarTool = new CalendarToolManager();
await calendarTool.InitializeAsync();

// 2. Add to your agent
agent.AddTool(calendarTool);
```

## Usage Examples

```csharp
// Schedule meetings
await agent.SendAsync("Schedule a meeting with John tomorrow at 2 PM");
await agent.SendAsync("Set up a team standup every Monday at 9 AM");

// Check availability
await agent.SendAsync("When is Sarah available this week?");
await agent.SendAsync("Find a 1-hour slot for our team this Friday");

// Query events
await agent.SendAsync("What meetings do I have next Monday?");
await agent.SendAsync("Show my calendar for this week");

// Update or cancel
await agent.SendAsync("Move my 2 PM meeting to 3 PM");
await agent.SendAsync("Cancel tomorrow's standup");
```

## Configuration

### Permissions Required

In Azure AD, grant:
- `Calendars.Read` - Read calendars
- `Calendars.ReadWrite` - Create and update events

### Time Zones

Set default time zone in **Project Settings → AIDevKit Enterprise**:

```
Time Zone: Pacific Standard Time
```

Or let users specify: "Schedule at 2 PM EST"

## Best Practices

1. **Be Specific**: Include date, time, and duration
2. **Time Zones**: Always specify time zone for clarity
3. **Attendees**: Mention who should be invited
4. **Conflicts**: Agent will check availability automatically

## Troubleshooting

### Meeting Not Created

**Problem**: Agent confirms but event doesn't appear

**Solutions**:
- Check calendar permissions
- Verify attendee email addresses
- Ensure calendar sync is enabled

### Wrong Time Zone

**Problem**: Meeting created in wrong time zone

**Solutions**:
- Set default time zone in settings
- Specify time zone in request ("2 PM PST")
- Check Graph API time zone configuration

## Related Topics

- [Authentication & Setup](authentication.md)
- [Microsoft Graph](README.md)
- [Mail Tool](mail-tool.md)
