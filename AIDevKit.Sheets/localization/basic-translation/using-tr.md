# Using .Tr()

## Syntax

```csharp
string result = key.Tr(tableName);
```

### Parameters

- **key** (`string`): The translation key (e.g., "menu.title")
- **tableName** (`string`, optional): The localization table name

## Return Type

`.Tr()` returns a `TrStringTask` which has implicit conversion to `string`.

## Examples

### With Table Name

```csharp
string title = "menu.title".Tr("UI");
string description = "item.apple.desc".Tr("Items");
```

### Without Table Name

```csharp
// Uses default table (if configured)
string text = "common.ok".Tr();
```

## Implicit Conversion

The task automatically converts to string when assigned:

```csharp
// Direct assignment
string text = "menu.title".Tr("UI");

// In Unity UI
label.text = "welcome.message".Tr("UI");
tmpText.text = "score.label".Tr("UI");
```

## Explicit Value Access

For more control, access `.value` property:

```csharp
var task = "menu.title".Tr("UI");
string text = task.value;
```

## Chaining with Other Methods

Since `.Tr()` returns a task, you can chain additional operations:

```csharp
string text = "welcome.message"
    .Tr("UI")
    .Format("PlayerName"); // String formatting

string plural = "item.apple"
    .Tr("Items")
    .Count(5); // Pluralization
```

## Performance Notes

- The first access resolves and caches the value
- Subsequent accesses return the cached result
- Create the task once and reuse when possible

## Common Patterns

### UI Text Assignment

```csharp
public class MenuUI : MonoBehaviour
{
    public TextMeshProUGUI titleText;
    public TextMeshProUGUI descriptionText;

    void Start()
    {
        titleText.text = "menu.title".Tr("UI");
        descriptionText.text = "menu.description".Tr("UI");
    }
}
```

### Dynamic Content

```csharp
void UpdateItemDisplay(string itemKey)
{
    string name = $"item.{itemKey}.name".Tr("Items");
    string desc = $"item.{itemKey}.description".Tr("Items");
    
    nameLabel.text = name;
    descLabel.text = desc;
}
```

### Conditional Text

```csharp
string GetStatusText(bool isOnline)
{
    return isOnline 
        ? "status.online".Tr("UI") 
        : "status.offline".Tr("UI");
}
```
