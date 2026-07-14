# Getting Complier Errors after AI Dev Kit update

### **Issue**

{% hint style="danger" %}
After updating **AI Dev Kit** to a newer version, you encounter one or many compile errors.
{% endhint %}

### **Possible Cause**

{% hint style="info" %}
The Unity Package Manager often fails to track deleted files when updating the **AI Dev Kit**, which can result in **duplicate classes, compiler errors, or unexpected behavior**.\
To avoid this, it's recommended to **completely remove the previous version** of the AI Dev Kit before importing the new one.
{% endhint %}

### **How to Fix**

{% hint style="success" %}
In the Project window, delete the entire `Assets/Glitch9` folder, then re-import **AI Dev Kit** from the Package Manager or your downloaded package.\
This ensures outdated files are fully removed before importing the latest version.
{% endhint %}

### **If It Still Doesn’t Work**

{% hint style="success" %}
Confirm that you’re importing the most recent release of **AI Dev Kit**. Version info is available in the Unity Package Manager.
{% endhint %}

{% hint style="info" %}
If the issue persists even with the latest version, consider joining [Discord](https://discord.gg/hgajxPpJYf) — where I provide **preview builds**, **hotfixes**, and direct support.&#x20;

🔒 **Gain** **Access to the Agent preview builds channel.**\
To gain access, please send me your **proof of purchase** (e.g. invoice or order number).
{% endhint %}
