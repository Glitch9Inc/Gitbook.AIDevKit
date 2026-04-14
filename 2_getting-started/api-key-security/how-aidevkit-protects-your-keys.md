---
description: A deep dive into SecureToken — the XOR-based obfuscation system that keeps your keys out of plain sight
---

# How AIDevKit Protects Your Keys

AIDevKit stores API keys through the `SecureToken` class, which applies a multi-step obfuscation scheme before writing anything to disk. This page explains exactly how that scheme works and where its limits are, so you can make an informed decision about your threat model.

---

## The Encryption Pipeline

When you enter an API key in the AIDevKit settings window, the key is processed through the following steps before being serialized into a Unity ScriptableObject:

### Step 1 — XOR Cipher

The raw key is XOR'd character-by-character against a secret mask:

```
Original key:  "sk-1234567890abcdef"
XOR mask:      "s3cr3t"  (cycled to match the key length)
──────────────────────────────────────
Cipher:        [binary data — unreadable]
```

XOR is symmetric: applying the same mask a second time recovers the original key. This is the same operation used for both encryption and decryption.

The mask itself (`s3cr3t`) is never stored as a single literal in the source code. It is assembled at runtime from two private compile-time constants:

```csharp
private const string M_PART_A = "s3c";
private const string M_PART_B = "r3t";

public static string GetMask() => M_PART_A + M_PART_B;
```

Splitting the mask across two constants means a simple string search for `s3cr3t` inside the compiled binary will fail — the attacker must locate and join both halves.

### Step 2 — Hex Encoding

The raw binary cipher is converted to a printable hexadecimal string:

```
Cipher (binary):  [raw bytes]
Hex string:       "1A0F324B012C6E..."
```

This makes the encrypted key safe to store as a plain text serialized field without encoding issues.

### Step 3 — Split Storage

The hex string is cut exactly in half and stored as **two separate serialized fields** in the ScriptableObject, named `ambientFactor` and `reflectionIndex`:

```
Hex string:       "1A0F32 | 4B012C6E"
                     ↓           ↓
ambientFactor:   "1A0F32"     (stored in asset file)
reflectionIndex: "4B012C6E"   (stored in asset file)
```

No single field in the asset file contains the complete encrypted token. An attacker who reads the serialized asset sees two meaningless, incomplete hex strings with no obvious labels.

### Decryption at Runtime

Decryption happens automatically on demand and is never cached in memory:

```
ambientFactor + reflectionIndex  →  rejoin
     ↓
FromHex  →  XOR with mask  →  Original API key ✅
```

The key is passed **directly** to the HTTP request header without storing it in a local variable, so it exists in managed memory for the shortest possible time.

---

## Why This Raises the Bar for Attackers

| Layer | What it defeats |
|---|---|
| **XOR obfuscation** | Plain-text grep of asset files (`sk-...` patterns) will find nothing |
| **Hex encoding** | The stored value looks like random data, not an API key |
| **Split into two fields** | Neither field alone is enough to reconstruct the key |
| **Mask split into two constants** | A single-string search in the compiled binary will not find the mask |
| **No in-memory caching** | Memory snapshots taken outside the brief decryption window will not contain the plaintext key |

Casual reverse-engineering — the most common threat for distributed games and tools — is substantially harder because there is no single place where the key can be found by pattern matching.

---

## Why This Is Not 100% Protection

`SecureToken` is an **obfuscation layer**, not military-grade encryption. A determined attacker with the right tools can still breach it:

**The mask is compiled into the binary.**\
IL2CPP-compiled Unity builds can be partially reversed with tools like *Il2CppDumper*. Mono builds are even more accessible via *dnSpy* or *ILSpy*. Once the attacker recovers the values of `M_PART_A` and `M_PART_B`, they can reconstruct the mask and decrypt any key stored in any asset file.

**XOR with a static, short mask is not cryptographically secure.**\
The mask (`s3cr3t`) is only 6 characters long. Unlike AES or ChaCha20, XOR with a short repeated mask does not provide forward secrecy, key derivation, or resistance to known-plaintext attacks. Anyone who can guess a few characters of your API key (e.g., the well-known `sk-` prefix for OpenAI keys) can recover the corresponding mask bytes immediately.

**Memory scanning still works during the decryption window.**\
Even though the key is not cached, it does pass through managed memory at the moment it is passed to `UnityWebRequest`. A process memory dump taken at that exact moment will contain the plaintext key.

**The protection is circumventable, not unbreakable.**\
The goal of `SecureToken` is to eliminate *easy*, *automated*, and *passive* extraction (file dumps, string searches, casual decompilation). It is not designed to stop a skilled, targeted reverse-engineer with full access to the binary.

> **Bottom line:** `SecureToken` is significantly better than storing keys in plain text, but it is not a substitute for a server-side architecture when the key being protected has high monetary or security value.
