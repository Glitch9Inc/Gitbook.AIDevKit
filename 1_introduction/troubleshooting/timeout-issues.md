# Timeout Issues

### **Issue**

{% hint style="danger" %}
When sending a request, it may hang for an extended period and eventually result in a **timeout error**.
{% endhint %}

### **Possible Cause**

{% hint style="info" %}
* This typically indicates a network issue, an overloaded server, or an invalid request that the API is struggling to process.
{% endhint %}

### **How to Fix**

{% hint style="success" %}
* Visit the the API's status page and see if the API is available.
* **Adjust the timeout setting**\
  **AI Dev Kit** includes a configurable timeout option to prevent requests from hanging indefinitely.\
  You can increase this limit under:\
  `Edit ▶ Preferences ▶ AI Dev Kit ▶ Timeout (seconds)`\
  A longer timeout may allow slower responses to complete successfully.
* **Retry after a short delay**\
  Sometimes, temporary server-side outages can cause requests to fail. If everything else looks fine, try again in a few minutes.
{% endhint %}

{% hint style="warning" %}
**If you're in China**\
OpenAI’s services may be restricted in your region. As reported by Yahoo! Finance, some apps and platforms have been ordered to block ChatGPT access. If you're in mainland China, this could be the root cause.
{% endhint %}

{% hint style="info" %}
If the issue persists even with the latest version, consider joining [Discord](https://discord.gg/hgajxPpJYf) — where I provide **preview builds**, **hotfixes**, and direct support.&#x20;

🔒 **Gain** **Access to the Agent preview builds channel.**\
To gain access, please send me your **proof of purchase** (e.g. invoice or order number).
{% endhint %}

