# Child Keys

The `.Child()` method allows you to append a sub-key to construct hierarchical translation keys.

## Syntax

```csharp
string result = parentKey.Tr(table).Child(childKey);
```

## How It Works

`.Child()` appends the child key with a dot:

```csharp
"item.apple".Tr("Items").Child("name")
// Resolves to: "item.apple.name"

"item.apple".Tr("Items").Child("description")
// Resolves to: "item.apple.description"
```

## Use Cases

### Structured Data

When your localization table has nested structure:

