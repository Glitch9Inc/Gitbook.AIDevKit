# Mail Tool

Send, read, and manage emails through natural language.

## Overview

The Mail Tool enables AI agents to interact with Microsoft 365 email - send messages, read inbox, search emails, and manage folders.

## Setup

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;

// 1. Create and initialize the tool
var mailTool = new MailToolManager();
await mailTool.InitializeAsync();

// 2. Add to your agent
agent.AddTool(mailTool);
```

## Usage Examples

```csharp
// Send emails
await agent.SendAsync("Send an email to team@contoso.com about tomorrow's meeting");
await agent.SendAsync("Email John thanking him for the report");

// Read emails
await agent.SendAsync("What unread emails do I have?");
await agent.SendAsync("Show me emails from this week");

// Search emails
await agent.SendAsync("Find emails about the Q4 budget");
await agent.SendAsync("Search for messages from Sarah last month");

// Manage emails
await agent.SendAsync("Move this email to Archive");
await agent.SendAsync("Reply to John's latest email");
```

## Configuration

### Permissions Required

In Azure AD, grant:
- `Mail.Read` - Read emails
- `Mail.Send` - Send emails
- `Mail.ReadWrite` - Full email access

### Email Formatting

The agent automatically formats emails appropriately. For HTML content, just describe what you want:

```csharp
await agent.SendAsync("Send a styled email to marketing with bold title");
```

## Best Practices

1. **Clear Recipients**: Specify email addresses or names clearly
2. **Context**: Provide enough context for the email content
3. **Attachments**: Mention specific files to attach
4. **Urgency**: Indicate if email is urgent or important

## Troubleshooting

### Email Not Sent

**Problem**: Agent confirms but email not in Sent folder

**Solutions**:
- Check Mail.Send permission
- Verify recipient addresses
- Check email size limits (<25 MB)

### Search No Results

**Problem**: Search returns no emails

**Solutions**:
- Make search terms more specific
- Check date range
- Verify folder access

### Attachment Issues

**Problem**: Attachments not included

**Solutions**:
- Ensure file path is accessible
- Check file size (<4 MB for direct attach)
- Use OneDrive links for large files

## Related Topics

- [Authentication & Setup](authentication.md)
- [Microsoft Graph](README.md)
- [OneDrive Tool](onedrive-tool.md)
