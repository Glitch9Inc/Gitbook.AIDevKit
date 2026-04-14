# OneDrive Tool

Access and manage OneDrive files through natural language.

## Overview

The OneDrive Tool enables AI agents to interact with OneDrive - search files, upload/download, and manage folders.

## Setup

```csharp
using Glitch9.AIDevKit.Microsoft.Graph.Tools;

// 1. Create and initialize the tool
var oneDriveTool = new OneDriveToolManager();
await oneDriveTool.InitializeAsync();

// 2. Add to your agent
agent.AddTool(oneDriveTool);
```

## Usage Examples

```csharp
// Search and find files
await agent.SendAsync("Find all Excel files about Q4 budget");
await agent.SendAsync("Where is the project proposal document?");

// Upload files
await agent.SendAsync("Upload report.pdf to the Documents folder");
await agent.SendAsync("Save this file to OneDrive");

// Download files
await agent.SendAsync("Download the latest marketing presentation");

// Share files
await agent.SendAsync("Create a sharing link for the Q4 report");
await agent.SendAsync("Share budget.xlsx with view-only access");

// Organize files
await agent.SendAsync("Create a folder called Q4 Reports");
await agent.SendAsync("Move the proposal to the Projects folder");
```

## Configuration

### Permissions Required

In Azure AD, grant:
- `Files.Read.All` - Read files
- `Files.ReadWrite.All` - Full file access

### Large Files

Files over 4MB are automatically handled with upload sessions - no special configuration needed.

## Best Practices

1. **Specific Paths**: Use clear folder names and paths
2. **File Names**: Be specific about file names when searching
3. **Sharing**: Specify view-only or edit permissions clearly
4. **Organization**: Use folders to keep files organized

## Troubleshooting

### File Not Found

**Problem**: Agent can't find the file

**Solutions**:
- Check file name spelling
- Verify file location
- Ensure OneDrive sync is complete
- Check file permissions

### Upload Failed

**Problem**: File upload doesn't complete

**Solutions**:
- Check OneDrive storage space
- Verify file format is supported
- For large files, ensure stable connection
- Check file isn't locked by another app

### Permission Denied

**Problem**: "Access denied" error

**Solutions**:
- Verify API permissions in Azure AD
- Check file/folder permissions
- Ensure user owns the file
- Request access from file owner

## Related Topics

- [Authentication & Setup](authentication.md)
- [Microsoft Graph](README.md)
- [SharePoint Tool](sharepoint-tool.md)
