# Best Practices

The following recommendations are ranked roughly from highest to lowest impact. Apply as many as your project allows.

---

## 1. Always Use `SecureToken` for Client-Side Storage

If a server-side proxy is not possible, never store API keys as plain strings — in source code, in `PlayerPrefs`, or in a plain text asset file. Always use AIDevKit's `SecureToken`, which applies XOR obfuscation, hex encoding, and split-field storage to prevent trivial extraction.

See [How AIDevKit Protects Your Keys](how-aidevkit-protects-your-keys.md) for a full explanation.

---

## 2. Create Restricted API Keys

Most providers let you create keys scoped to specific operations. Issue the **minimum set of permissions** your application actually needs:

* An app that only does chat completions should not have a key that can also manage fine-tunes, create vector stores, or generate images.
* A key that can only read cannot be used to cause write-side damage.

Check your provider's dashboard for scope/permission settings before distributing a key.

---

## 3. Set Spending Caps and Rate Limits

Even a stolen, fully functional key does limited damage if you have configured hard usage ceilings:

* Set a **monthly budget cap** on the provider dashboard — API calls stop once the cap is hit.
* Set a **per-minute or per-day rate limit** where the provider supports it.
* Enable **email or webhook alerts** for anomalous usage spikes.

These controls do not prevent theft, but they dramatically contain the blast radius.

---

## 4. Never Commit Keys to Version Control

Add your API key configuration files to `.gitignore` before your first commit. A key committed to a public repository is typically found and abused within minutes by automated scanning bots.

```gitignore
# .gitignore
Assets/Resources/APIKeys.asset
Assets/Resources/ApiConfig.asset
*.env
```

For extra safety, use a tool like [git-secrets](https://github.com/awslabs/git-secrets) or a pre-commit hook that rejects commits containing known key patterns.

---

## 5. Use a Separate Key Per Environment

Maintain distinct keys for development, staging, and production:

| Environment | Key scope | Notes |
|---|---|---|
| Development | Broad (for convenience) | Never shipped to users |
| Staging / QA | Restricted | Close to production rules |
| Production | Minimal permissions + hard spending cap | Rotated on a schedule |

This way, a leaked development key cannot be used against production, and a production incident does not affect your development workflow.

---

## 6. Rotate Keys Regularly

Establish a rotation schedule — monthly or quarterly — and immediately rotate any key that may have been exposed (accidental commit, team member departure, suspected breach). Most providers let you generate a replacement key before revoking the old one, giving you a zero-downtime rotation window.

---

## 7. Monitor Usage for Anomalies

Set up automated monitoring on your provider dashboard or billing page to detect abnormal patterns:

* Volume spikes outside expected hours
* Requests from unexpected IP ranges or geographies
* Model or endpoint usage outside your application's scope

Early detection limits financial damage and lets you revoke the key before significant harm is done.

---

## 8. Do Not Log or Transmit Keys

Be careful that keys are never accidentally written to:

* Log files (`Debug.Log`, analytics events, crash reporters)
* Network requests beyond the intended API call (e.g., debug payloads)
* Local save files or `PlayerPrefs`

When passing keys to HTTP headers, pass them inline rather than storing them in intermediate variables to minimize the window of in-memory exposure.

---

## Summary Checklist

- [ ] Store client-side keys with `SecureToken`, never as plain strings
- [ ] Create restricted-scope keys for each application
- [ ] Set spending caps and usage alerts on the provider dashboard
- [ ] Add key asset files to `.gitignore`
- [ ] Maintain separate keys for dev, staging, and production
- [ ] Rotate keys on a schedule and immediately after any suspected exposure
- [ ] Monitor usage dashboards for anomalies
- [ ] Confirm keys are never written to logs or save data
