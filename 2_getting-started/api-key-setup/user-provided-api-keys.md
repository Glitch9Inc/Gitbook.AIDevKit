---
description: Let each user provide and use their own API keys at runtime
icon: user
---

# User-Provided API Keys

If your app lets users bring their own API keys, you can connect your user account system to AIDevKit through `AIDevKitClient.CurrentUser`.

When `CurrentUser` is set, AIDevKit can:

- Use each user's own API key
- Apply per-user service tiers
- Attach a user-safe identifier to requests
- Personalize AI behavior with profile context

***

## 1. Implement `IUserProfile`

Use your existing user model (or a wrapper) and implement `IUserProfile`.

```csharp
using Glitch9.AIDevKit;
using System.Collections.Generic;

public class AppUser : IUserProfile
{
    public string Id { get; set; }
    public string Name { get; set; }
    public string Occupation { get; set; }
    public Dictionary<string, string> AdditionalInfo { get; set; }
    public Location Location { get; set; }

    // Return a user-specific API key, or null to use project-level settings.
    public string GetApiKey(Api api)
    {
        return myUserDatabase.GetApiKey(Id, api);
    }

    // Return a user-specific service tier, or null for default behavior.
    public ServiceTier? GetServiceTier(Api api)
    {
        return myUserDatabase.GetServiceTier(Id, api);
    }
}
```

***

## 2. Set `CurrentUser` after login

Set it once when authentication finishes, and clear it on logout.

```csharp
void OnUserLoggedIn(AppUser user)
{
    AIDevKitClient.CurrentUser = user;
}

void OnUserLoggedOut()
{
    AIDevKitClient.CurrentUser = null;
}
```

{% hint style="info" %}
`CurrentUser` is static. After it is set, all AI requests in the session automatically use it.
{% endhint %}

***

## 3. How API key resolution works

AIDevKit resolves keys in this order:

1. `CurrentUser.GetApiKey(api)` (user key)
2. Project key in **Edit > Preferences > AI Dev Kit** (fallback)

If `GetApiKey` returns `null`, AIDevKit safely falls back to the project key.

***

## Minimal setup example

If you only need user identification and not per-user keys:

```csharp
public class SimpleUser : IUserProfile
{
    public string Id { get; set; }
    public string Name { get; set; }
    public string Occupation => null;
    public Dictionary<string, string> AdditionalInfo => null;
    public Location Location => default;
    public string GetApiKey(Api api) => null;
    public ServiceTier? GetServiceTier(Api api) => null;
}
```
