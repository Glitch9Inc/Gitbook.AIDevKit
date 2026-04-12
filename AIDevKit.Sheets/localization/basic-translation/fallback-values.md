# Fallback Values

The `.Fallback()` method provides a default value when a translation key is missing or invalid.

## Syntax

```csharp
string result = key.Tr(table).Fallback(defaultValue);
```

## Basic Usage

```csharp
// If "missing.key" doesn't exist, returns "Default Text"
string text = "missing.key".Tr("UI").Fallback("Default Text");

// If key exists, returns the translated value
string text = "menu.title".Tr("UI").Fallback("Untitled");
```

## When Fallback Is Used

Fallback is returned when:
1. The key doesn't exist in the table
2. The column for current locale is missing
3. The cell value is null or empty

## Use Cases

### Missing Keys

Provide graceful degradation:

```csharp
string GetItemName(string itemKey)
{
    return $"item.{itemKey}.name"
        .Tr("Items")
        .Fallback("Unknown Item");
}
```

### Development Placeholders

Use fallbacks during development:

```csharp
// Translation not ready yet
string tooltip = "feature.new.tooltip"
    .Tr("UI")
    .Fallback("[Tooltip Coming Soon]");
```

### User-Generated Content

Handle dynamic content safely:

```csharp
string GetCustomName(string userKey)
{
    return userKey.Tr("UserContent").Fallback(userKey);
    // Returns the key itself if translation missing
}
```

## Chaining

Fallback can be combined with other methods:

```csharp
string text = "item.apple"
    .Tr("Items")
    .Child("description")
    .Locale(Locale.KoKR)
    .Fallback("No description available");
```

## Dynamic Fallbacks

Generate fallback values dynamically:

```csharp
string GetLocalizedText(string key, string table)
{
    string fallback = $"[Missing: {key}]";
    return key.Tr(table).Fallback(fallback);
}
```

## Empty String vs Fallback

```csharp
// Empty string in table cell returns empty string
string text1 = "empty.key".Tr("UI").Fallback("Fallback");
// Returns: "" (if cell exists but is empty)

// Missing key returns fallback
string text2 = "missing.key".Tr("UI").Fallback("Fallback");
// Returns: "Fallback"
```

## Best Practices

### Meaningful Fallbacks

Provide fallbacks that make sense in context:

```csharp
// Good: descriptive fallback
string name = itemKey.Tr("Items").Fallback("Unnamed Item");

// Bad: technical fallback
string name = itemKey.Tr("Items").Fallback("ERROR_TRANSLATION_MISSING");
```

### Debug Mode

Use fallbacks to identify missing translations during development:

```csharp
#if UNITY_EDITOR
string text = key.Tr(table).Fallback($"[MISSING: {table}.{key}]");
#else
string text = key.Tr(table).Fallback("");
#endif
```

### UI Default Text

Set fallback to match your UI's default state:

```csharp
public class HealthBar : MonoBehaviour
{
    public TextMeshProUGUI label;

    void Start()
    {
        label.text = "ui.health.label"
            .Tr("UI")
            .Fallback("Health"); // Matches default Prefab text
    }
}
```

## Performance

Fallback values are only evaluated when needed (lazy evaluation):

```csharp
// This expensive call only runs if key is missing
string text = "menu.title".Tr("UI").Fallback(ExpensiveStringGeneration());
```
