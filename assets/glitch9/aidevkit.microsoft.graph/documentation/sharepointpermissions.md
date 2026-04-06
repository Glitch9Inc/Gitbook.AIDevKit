# SharePoint API Permissions (Application Only)

## Which Permission Should I Use?

### 📄 Read Files Only

**Permission:** `Sites.Read.All`\
Allows listing sites and downloading files only

### 📝 Read + Write Files

**Permission:** `Sites.ReadWrite.All`\
Allows upload, download, and delete operations ← **Use this for most cases**

### 🔒 Specific Site Only (Recommended)

**Permission:** `Sites.Selected`\
Allows access to a single site only (most secure)

***

## Reference Links

* [Official Documentation](https://learn.microsoft.com/graph/permissions-reference)
