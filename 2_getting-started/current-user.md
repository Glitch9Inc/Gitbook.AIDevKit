---
description: Link your game's user account system to AIDevKit for per-user API keys, personalization, and safety tracking
icon: user
---

# Current User

## What is `CurrentUser`?

`AIDevKitClient.CurrentUser` is a static property that lets you tell AIDevKit which user is currently logged in to your application.

```csharp
AIDevKitClient.CurrentUser = myLoggedInUser; // implements IUserProfile
```

Setting it is completely optional. When it is set, AIDevKit uses the user's information to:

- **Use the user's own API key** — if your app lets users bring their own key
- **Select the correct service tier** — if users can subscribe to different AI tiers
- **Personalize AI interactions** — name, occupation, and extra context flow into every AI request
- **Generate a safe safety identifier** — attached to every outgoing API call for abuse monitoring

---

## Implementing `IUserProfile`

Attach `IUserProfile` to your existing user class (or a dedicated wrapper):

```csharp
using Glitch9.AIDevKit;
using System.Collections.Generic;

public class AppUser : IUserProfile
{
    // Required: unique user ID used for safety tracking
    public string Id { get; set; }

    // AI models will address or refer to the user by this name
    public string Name { get; set; }

    // Provides professional context to AI responses
    public string Occupation { get; set; }

    // Any freeform key/value pairs (interests, preferences, etc.)
    public Dictionary<string, string> AdditionalInfo { get; set; }

    // Used for web-search-aware features (optional)
    public Location Location { get; set; }

    // Return the user's own API key, or null to fall back to the project key
    public string GetApiKey(Api api)
    {
        return myUserDatabase.GetApiKey(userId, api); // your own lookup
    }

    // Return the user's service tier, or null to use the project default
    public ServiceTier? GetServiceTier(Api api)
    {
        return myUserDatabase.GetServiceTier(userId, api); // your own lookup
    }
}
```

Only implement the members you need. Return `null` from `GetApiKey` and `GetServiceTier` to let AIDevKit fall back to the project-level settings.

---

## Setting `CurrentUser`

Call this after your authentication flow completes (login, session restore, etc.):

```csharp
// On login
void OnUserLoggedIn(AppUser user)
{
    AIDevKitClient.CurrentUser = user;
}

// On logout
void OnUserLoggedOut()
{
    AIDevKitClient.CurrentUser = null;
}
```

{% hint style="info" %}
`CurrentUser` is a static property. You only need to set it once per session. All subsequent AI requests will automatically pick it up.
{% endhint %}

---

## What Changes When `CurrentUser` Is Set?

### Per-User API Keys

`AIClient` checks `CurrentUser.GetApiKey(api)` before falling back to the project-level key stored in Editor Preferences. This lets you ship an app where players supply their own API keys without exposing yours.

```csharp
// AIDevKit internally resolves the API key in this order:
// 1. CurrentUser.GetApiKey(api)   ← user's own key
// 2. Project-level key in settings ← your key
```

### Per-User Service Tier

Some providers (e.g. OpenAI) support different rate limits or features depending on the account's service tier. If your app offers tiered subscriptions, implement `GetServiceTier` to have each user's requests routed with the correct tier header.

### Safety Identifier

Every outgoing API request includes a `user` field that AI providers use for abuse detection. AIDevKit resolves this automatically:

| Condition | SafetyIdentifier value |
|---|---|
| `CurrentUser.Id` is set | The user's ID |
| Not set, but `Application.identifier` exists | Your app's bundle ID |
| Neither is available | `"My Unity Project"` |

You never need to set this manually — it is derived from `CurrentUser` at request time.

### AI Personalization

When `Name`, `Occupation`, or `AdditionalInfo` are populated, AIDevKit can inject them into the system prompt so that AI responses feel personalized to the current player or user.

---

## Minimal Example

If you only need the safety identifier and don't require per-user keys or tiers:

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

// On login
AIDevKitClient.CurrentUser = new SimpleUser
{
    Id = firebaseUser.Uid,
    Name = firebaseUser.DisplayName
};
```

---

## Summary

| Property / Method | Purpose | Required |
|---|---|---|
| `Id` | Safety identifier for API monitoring | Recommended |
| `Name` | Personalize AI responses | Optional |
| `Occupation` | Extra context for AI | Optional |
| `AdditionalInfo` | Freeform user context | Optional |
| `Location` | Web search context | Optional |
| `GetApiKey(api)` | Per-user API key lookup | Optional |
| `GetServiceTier(api)` | Per-user service tier | Optional |
