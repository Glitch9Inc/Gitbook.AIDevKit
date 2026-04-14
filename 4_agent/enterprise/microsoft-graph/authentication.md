# Authentication & Setup

Configure Microsoft Graph authentication for enterprise integrations.

## Prerequisites

- Azure AD subscription
- Admin access to register apps
- AI Dev Kit Enterprise tier

## Step 1: Register Azure AD Application

### Create App

1. Go to [Azure Portal](https://portal.azure.com)
2. **Azure Active Directory** → **App registrations** → **New registration**
3. Name: `AIDevKit Enterprise`
4. Save these values:
   - **Application (client) ID**
   - **Directory (tenant) ID**

### Create Client Secret

1. Go to **Certificates & secrets**
2. **New client secret**
3. **Save the value immediately** (won't see it again)

## Step 2: Configure Permissions

### Add Permissions

1. Go to **API permissions** → **Add a permission** → **Microsoft Graph**
2. Choose **Application permissions** (for background/daemon apps)
3. Add permissions for your tools:

**SharePoint Tool:**
- Sites.Read.All
- Sites.ReadWrite.All

**Calendar Tool:**
- Calendars.ReadWrite

**Mail Tool:**
- Mail.ReadWrite
- Mail.Send

**OneDrive Tool:**
- Files.ReadWrite.All

**Todo Tool:**
- Tasks.ReadWrite

4. Click **Grant admin consent for [Your Organization]**

## Step 3: Configure in Unity

### Option A: Project Settings

**Edit → Project Settings → AIDevKit Enterprise**

```
Client ID: [paste your Application ID]
Tenant ID: [paste your Directory ID]  
Client Secret: [paste your Client Secret]
```

### Option B: Code Configuration

```csharp
using Glitch9.AIDevKit.Microsoft.Graph;

var graphClient = new GraphClient(
    clientId: "your-client-id",
    tenantId: "your-tenant-id",
    clientSecret: "your-client-secret"
);

GraphClient.DefaultInstance = graphClient;
```

## Step 4: Test Authentication

```csharp
using UnityEngine;

public class AuthTest : MonoBehaviour
{
    async void Start()
    {
        try
        {
            var client = GraphClient.DefaultInstance;
            await client.AuthenticateAsync();
            
            Debug.Log("✓ Authentication successful!");
        }
        catch (Exception ex)
        {
            Debug.LogError($"✗ Authentication failed: {ex.Message}");
        }
    }
}
```

## User Authentication (Alternative)

For user-facing apps where you want to act on behalf of a signed-in user:

```csharp
var graphClient = new GraphClient(
    clientId: "your-client-id",
    tenantId: "your-tenant-id",
    redirectUri: "http://localhost:8080",
    scopes: new[] { "User.Read", "Sites.Read.All" }
);

// Opens browser for user sign-in
await graphClient.AuthenticateInteractiveAsync();
```

## Security Best Practices

### Never Commit Secrets

```csharp
// ❌ DON'T: Hardcode secrets
var client = new GraphClient("app-id", "tenant", "my-secret-123");

// ✅ DO: Use secure storage
var secret = PlayerPrefs.GetString("GraphClientSecret");
var client = new GraphClient(appId, tenantId, secret);
```

### Secure Storage Options

1. **PlayerPrefs** (encrypted in builds)
2. **Environment Variables**
3. **Azure Key Vault** (production)
4. **Unity Cloud Build Secrets**

## Troubleshooting

### Invalid Client Secret

```
Error: AADSTS7000215: Invalid client secret
```

**Fix**: Generate new secret in Azure Portal

### Insufficient Permissions

```
Error: Insufficient privileges
```

**Fix**: 
- Add required permissions in Azure AD
- Grant admin consent
- Wait 5-10 minutes for permissions to propagate

### Redirect URI Mismatch

```
Error: AADSTS50011: Reply URL does not match
```

**Fix**: Add redirect URI in Azure AD app configuration

## Next Steps

Choose the tools you need:
- [SharePoint Tool](sharepoint-tool.md)
- [Calendar Tool](calendar-tool.md)
- [Mail Tool](mail-tool.md)
- [OneDrive Tool](onedrive-tool.md)
- [Todo Tool](todo-tool.md)

## Related Topics

- [Microsoft Graph Overview](README.md)
- [Enterprise Integrations](../README.md)
