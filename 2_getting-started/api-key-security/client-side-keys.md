---
description: Why API keys are vulnerable in client-side applications, and how SecureToken is your first line of defense
---

# Client-Side Keys

## The Core Problem

When you ship a Unity application, every file bundled with it is physically on the player's machine — ScriptableObjects, compiled bytecode, serialized assets, all of it. **Anyone who has your app can inspect it.**

This creates an inherent tension: AIDevKit needs your API key to call AI providers at runtime, but storing that key anywhere in the client makes it technically accessible to an attacker. There is no way to completely eliminate this tension without moving the key off the client entirely — which is not always practical for the kinds of tools AIDevKit is designed for.

The realistic goal, then, is not perfect security but **raising the cost of extraction** high enough that casual and automated attacks fail.

---

## How Attackers Extract Client-Side Keys

Understanding the attack surface helps you understand why plain-text storage is so dangerous:

| Attack method | What an attacker does |
|---|---|
| **Strings extraction** | Runs `strings yourgame.exe` or greps the APK/IPA for patterns like `sk-...` or `AIza...` — takes seconds |
| **Asset file inspection** | Opens `.asset`, `.json`, or `.bytes` files with a hex editor — Unity serialized assets are human-readable |
| **IL decompilation** | Uses Il2CppDumper or dnSpy to recover string constants embedded in compiled code |
| **Memory scanning** | Uses Cheat Engine or `frida` to dump process memory at runtime and search for key patterns |

If your key is stored as a plain string constant anywhere — in source code, in a ScriptableObject, in `PlayerPrefs` — any of the above methods will expose it in minutes.

---

## SecureToken: AIDevKit's First Line of Defense

Rather than storing the raw key, AIDevKit uses `SecureToken` to obfuscate it through a multi-step pipeline before anything is written to disk:

1. **XOR cipher** — the key is XOR'd against a secret mask, producing unreadable binary data
2. **Hex encoding** — the binary data is converted to a printable hex string
3. **Split storage** — the hex string is cut in half and saved as two separate serialized fields (`ambientFactor` and `reflectionIndex`)

The result is that no single field in your ScriptableObject contains a recognizable key. Plain-text grep finds nothing. A hex editor shows meaningless fragments. The mask itself is never stored as a complete literal in the source — it is assembled from two separate compile-time constants at runtime.

This approach directly defeats the two most common attacks — strings extraction and asset inspection — because neither produces anything that looks like an API key.

For a complete technical breakdown of how the encryption pipeline works and where its limits are, see [How AIDevKit Protects Your Keys](how-aidevkit-protects-your-keys.md).

---

## What SecureToken Does Not Protect Against

`SecureToken` is an obfuscation layer, not an impenetrable vault. A patient, skilled attacker can still:

* **Reverse-engineer the binary** to recover both mask constants and reconstruct the XOR key
* **Scan memory at runtime** during the brief window when the key is decrypted and passed to the HTTP request
* **Patch the binary** to intercept the decrypted value before it leaves the process

This means `SecureToken` is effective against passive, automated, and casual attacks — but not against a determined, targeted reverse-engineer.

---

## Reducing Risk Beyond SecureToken

When the key must live in the client, layer these controls on top of `SecureToken`:

### Restrict Key Permissions

Most providers let you create keys scoped to specific endpoints. A key that can only call Chat Completions cannot be used to generate images, manage fine-tunes, or drain your storage. Issue the narrowest permission set your app actually requires.

### Set Hard Spending Caps

Configure a monthly budget ceiling on your provider dashboard. Even a fully compromised key cannot cost more than your cap — accidental or malicious overuse stops at the limit.

### Never Ship Your Own Key to End Users

If you are building a tool that other developers use (an editor extension, a Unity asset), require users to enter their own API key rather than bundling yours. Each developer is then responsible for their own key's security, and a breach of one installation does not affect others.

### Keep Keys Out of Version Control

Add your API key asset files to `.gitignore`. A key committed to any repository — especially a public one — is typically extracted and abused within minutes by automated scanning bots.
