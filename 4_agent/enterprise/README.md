# Enterprise Integrations

Enterprise-tier integrations for connecting AI agents with business platforms.

## Overview

Enterprise Integrations enable your AI agents to interact with Microsoft 365 services like SharePoint, Outlook, OneDrive, and more through natural language.

## Available Integrations

### Microsoft Graph

Connect to Microsoft 365 services:

- **[SharePoint](microsoft-graph/sharepoint-tool.md)** - Search company documents using RAG
- **[Calendar](microsoft-graph/calendar-tool.md)** - Schedule meetings and manage calendars
- **[Mail](microsoft-graph/mail-tool.md)** - Send and manage emails
- **[OneDrive](microsoft-graph/onedrive-tool.md)** - Access and organize files
- **[Todo](microsoft-graph/todo-tool.md)** - Create and track tasks

## Quick Start

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;
using Glitch9.AIDevKit.Agents;

// 1. Setup authentication (see Authentication guide)
var graphClient = new GraphClient(clientId, tenantId, clientSecret);
GraphClient.DefaultInstance = graphClient;

// 2. Create tools
var sharePointTool = new SharePointToolManager();
var calendarTool = new CalendarToolManager();
var mailTool = new MailToolManager();

// 3. Add to agent
agent.AddTool(sharePointTool);
agent.AddTool(calendarTool);
agent.AddTool(mailTool);

// 4. Use naturally
await agent.SendAsync("Find the Q4 report in SharePoint");
await agent.SendAsync("Schedule a team meeting tomorrow");
await agent.SendAsync("Send a summary email to the team");
```

## Prerequisites

- **AI Dev Kit Enterprise Tier** required
- **Azure AD Application** with proper permissions
- **Microsoft 365** subscription

## Setup Steps

1. [Configure Authentication](microsoft-graph/authentication.md)
2. Choose the tools you need
3. Add tools to your agent
4. Start using with natural language

## Use Cases

- **Knowledge Base Q&A**: Query company documents in SharePoint
- **Meeting Assistant**: Schedule and manage meetings automatically
- **Email Automation**: Respond to and manage emails
- **File Management**: Organize and search OneDrive files
- **Task Tracking**: Create and track team tasks

## Related Topics

- [Microsoft Graph Integration](microsoft-graph/README.md)
- [Agent Tools](../tools/README.md)
- [Authentication & Setup](microsoft-graph/authentication.md)
