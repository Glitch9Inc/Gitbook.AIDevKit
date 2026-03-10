# Client-Side Keys

## Why Storing API Keys on the Client Is Risky

When you ship an application — whether it's a Unity game, a mobile app, or a desktop tool — all files bundled with it are, in principle, accessible to the user running it. This includes ScriptableObjects, configuration files, and even compiled bytecode.

**Anyone who has your app can also have your key.** Here is why that matters:

| Threat | What an attacker can do |
|---|---|
| **Strings extraction** | Run `strings yourgame.exe` or open the APK/IPA to search for patterns like `sk-...`, `AIza...`, or `Bearer ...` |
| **IL decompilation** | Unity IL2CPP or Mono builds can be partially reversed with tools (Il2CppDumper, dnSpy) to recover constants embedded in source code |
| **Memory scanning** | At runtime, tools like Cheat Engine or `frida` can dump process memory and grep for known API-key patterns |
| **Asset file inspection** | Unity serialized assets (`.asset`, `.json`, `.bytes`) are trivially readable with any hex editor |

Once your key is extracted, the attacker can call the same API on your behalf — charged to **your** account.

---

## When You Have No Choice

The ideal solution is always a **server-side proxy**: your client calls your own backend, and the backend holds the key and forwards requests to the AI provider. The key never leaves your server.

However, there are legitimate situations where this architecture is not feasible:

* You are shipping an **offline** or **LAN-only** tool (no server available)
* You are building an **editor plugin** used only by developers on trusted machines
* Your project is a **prototype or internal tool** not intended for wide distribution
* Server infrastructure is beyond the current project budget/scope

In these cases, you cannot eliminate the risk — but you can reduce the cost of a potential breach:

### Limit Key Permissions

Most AI providers (OpenAI, Google, Anthropic, etc.) allow you to create keys with **restricted scopes** — for example, a key that can only call the Chat Completions endpoint and nothing else. A stolen restricted key causes far less damage than a stolen full-access key.

### Set Strict Usage Limits

Set **monthly spending caps** and **per-request rate limits** on your API key dashboard. Even if a key is stolen, the attacker cannot generate significant cost beyond your set ceiling.

### Use Short-Lived or Rotating Keys

Some providers offer token-based authentication with expiry. Where possible, prefer a key with a short lifespan and rotate it regularly. Pair this with automated alerts for anomalous usage.

### Avoid Hard-Coding the Key as a Plain String

Never do this:

```csharp
// ❌ BAD — visible in any decompiler and every asset file
private const string ApiKey = "sk-abc123...";
```

Instead, use AIDevKit's `SecureToken` system (see [How AIDevKit Protects Your Keys](how-aidevkit-protects-your-keys.md)) to at least prevent trivial extraction via plain-text search.

### Do Not Ship Developer Keys to End Users

If your tool targets other developers (e.g., an editor extension, a Unity asset), instruct users to **supply their own API key** rather than bundling yours. Each user is then responsible for their own key's security.
