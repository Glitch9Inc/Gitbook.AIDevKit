# Microsoft Graph Integration

Unified API gateway to Microsoft 365 services.

## Overview

Microsoft Graph integration enables AI agents to work with:

- SharePoint documents
- Outlook calendars and emails
- OneDrive files
- Microsoft To Do tasks

All through natural language conversations.

## Quick Setup

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;

// 1. Create Graph client
var graphClient = new GraphClient(
    clientId: "your-client-id",
    tenantId: "your-tenant-id",
    clientSecret: "your-client-secret"
);

// 2. Set as default
GraphClient.DefaultInstance = graphClient;

// 3. Create and add tools
var sharePointTool = new SharePointToolManager();
await sharePointTool.InitializeAsync();

agent.AddTool(sharePointTool);
```

## Available Tools

- **[SharePoint](sharepoint-tool.md)** - RAG-powered document search
- **[Calendar](calendar-tool.md)** - Meeting scheduling
- **[Mail](mail-tool.md)** - Email management
- **[OneDrive](onedrive-tool.md)** - File operations
- **[Todo](todo-tool.md)** - Task management

## Setup Steps

### 1. Register Azure AD Application

1. Go to [Azure Portal](https://portal.azure.com)
2. Azure Active Directory → App registrations → New registration
3. Save: Application ID, Tenant ID
4. Create client secret

### 2. Configure Permissions

Grant these permissions based on tools you'll use:

| Tool | Required Permissions |
|------|---------------------|
| SharePoint | Sites.Read.All, Sites.ReadWrite.All |
| Calendar | Calendars.ReadWrite |
| Mail | Mail.ReadWrite, Mail.Send |
| OneDrive | Files.ReadWrite.All |
| Todo | Tasks.ReadWrite |

Click **Grant admin consent** after adding permissions.

### 3. Configure in Unity

**Project Settings → AIDevKit Enterprise:**

```
Client ID: [Your Application ID]
Tenant ID: [Your Directory ID]
Client Secret: [Your Client Secret]
```

### 4. Use in Code

```csharp
// That's it! Now use naturally:
await agent.SendAsync("Find budget reports in SharePoint");
await agent.SendAsync("Schedule a meeting with the team");
await agent.SendAsync("Send status update email");
```

## Security Best Practices

### DO NOT commit secrets to source control

```csharp
// ❌ Bad
var client = new GraphClient("app-id", "tenant-id", "my-secret");

// ✅ Good - Use PlayerPrefs
var secret = PlayerPrefs.GetString("GraphClientSecret");

// ✅ Better - Environment variables
var secret = Environment.GetEnvironmentVariable("GRAPH_SECRET");
```

### Token Management

Tokens are automatically cached and refreshed - you don't need to handle this.

## Troubleshooting

### Authentication Failed

**Check:**
- Client ID and Tenant ID are correct
- Client secret hasn't expired
- Permissions are granted with admin consent

### Permission Denied

**Check:**
- Required permissions are added in Azure AD
- Admin consent is granted
- User/app has access to the resource

### Rate Limiting

Microsoft Graph has rate limits. The agent automatically handles retry logic.

## Related Topics

- [Authentication & Setup](authentication.md)
- [SharePoint Tool](sharepoint-tool.md)
- [Enterprise Overview](../README.md)
