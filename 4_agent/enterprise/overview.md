# Enterprise Integrations Overview

Connect AI agents with business platforms and services.

## What Are Enterprise Integrations?

Pre-built connectors that let your AI agents:

- Access enterprise data (SharePoint, OneDrive)
- Manage business workflows (Calendar, Mail, Tasks)
- Search information using RAG (semantic search)
- Perform actions on behalf of users

All through **natural language** - no complex API calls needed.

## How It Works

```
User: "Find the Q4 report"
  ↓
AI Agent (with Enterprise Tools)
  ↓
SharePoint Search
  ↓
Result: "Found Q4 Financial Report..."
```

The agent handles all the complexity - authentication, API calls, data formatting.

## Authentication

Two methods:

### Application Authentication (Recommended for background tasks)

```csharp
var graphClient = new GraphClient(
    clientId: "your-app-id",
    tenantId: "your-tenant-id",
    clientSecret: "your-secret"
);
```

### User Authentication (For user-specific operations)

```csharp
var graphClient = new GraphClient(
    clientId: "your-app-id",
    tenantId: "your-tenant-id",
    redirectUri: "http://localhost:8080",
    scopes: new[] { "User.Read", "Sites.Read.All" }
);

await graphClient.AuthenticateInteractiveAsync();
```

See [Authentication Guide](microsoft-graph/authentication.md) for details.

## Available Services

### Current

- **Microsoft Graph** - SharePoint, Calendar, Mail, OneDrive, Todo

### Coming Soon

- Google Workspace
- Salesforce
- Slack
- Atlassian

## Security

- **Encrypted Storage**: Credentials stored securely
- **Token Refresh**: Automatic token management
- **Least Privilege**: Request only needed permissions
- **Audit Logging**: Track all operations

## Requirements

- AI Dev Kit Enterprise tier
- Unity 2021.3+
- Dependencies: Newtonsoft.Json, UniTask

## Related Topics

- [Microsoft Graph](microsoft-graph/README.md)
- [Authentication Setup](microsoft-graph/authentication.md)
- [SharePoint Tool](microsoft-graph/sharepoint-tool.md)
