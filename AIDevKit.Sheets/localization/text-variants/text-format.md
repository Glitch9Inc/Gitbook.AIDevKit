# Text Format (.Short/.Long)

Text format variants provide different length versions of the same text.

## Syntax

```csharp
// Full method
string result = key.Tr(table).TextFormat(TextFormat.Short);

// Shortcuts
string result = key.Tr(table).Abbr();   // Abbreviated
string result = key.Tr(table).Short();  // Short
string result = key.Tr(table).Long();   // Long
```

## TextFormat Enum

```csharp
public enum TextFormat
{
    Abbreviated,
    Short,
    Long
}
```

## How It Works

Appends format suffixes:

```csharp
.Abbr()  → .abbreviated
.Short() → .short
.Long()  → .long
```

## Table Setup

| Key | English |
|-----|---------|
| stats.health | Health Points |
| stats.health.abbreviated | HP |
| stats.health.short | Health |
| menu.options | Options |
| menu.options.short | Opts |
| time.remaining | Time Remaining: {0} |
| time.remaining.short | Time: {0} |

## Examples

### Responsive UI

```csharp
public class ResponsiveLabel : MonoBehaviour
{
    public bool isCompactMode;
    
    void UpdateText(string key)
    {
        var task = key.Tr("UI");
        
        label.text = isCompactMode 
            ? task.Short() 
            : task.Long();
    }
}
```

### Stats Display

```csharp
public class StatsBar : MonoBehaviour
{
    public bool showAbbreviated;
    
    void UpdateHealth(int current, int max)
    {
        string label = "stats.health".Tr("UI");
        
        if (showAbbreviated)
        {
            label = "stats.health".Tr("UI").Abbr();
        }
        
        statText.text = $"{label}: {current}/{max}";
        // Full: "Health Points: 100/100"
        // Abbreviated: "HP: 100/100"
    }
}
```

### Mobile vs Desktop

```csharp
void SetupUI()
{
    bool isMobile = Application.isMobilePlatform;
    
    string optionsText = "menu.options"
        .Tr("UI")
        .TextFormat(isMobile ? TextFormat.Short : TextFormat.Long);
    
    optionsButton.text = optionsText;
}
```

## Use Cases

### Screen Size Adaptation

```csharp
void OnScreenSizeChanged()
{
    bool isNarrow = Screen.width < 800;
    
    foreach (var button in menuButtons)
    {
        var task = button.Key.Tr("UI");
        button.label.text = isNarrow ? task.Short() : task.Long();
    }
}
```

### Tooltip vs Label

```csharp
void SetupButton(string key)
{
    button.text = key.Tr("UI").Short();
    tooltip.text = key.Tr("UI").Long();
}
```

### Table Headers

```csharp
void CreateTableHeaders(bool isCompactView)
{
    var format = isCompactView ? TextFormat.Abbreviated : TextFormat.Long;
    
    foreach (var columnKey in columnKeys)
    {
        string headerText = columnKey.Tr("UI").TextFormat(format);
        CreateHeader(headerText);
    }
}
```

## Combining with Other Variants

```csharp
string text = "item.potion"
    .Tr("Items")
    .Count(3)
    .Short()
    .Locale(Locale.KoKR);
```

## Fallback Behavior

If the format variant doesn't exist, uses the base key:

```csharp
// Table only has "menu.help" (no .short variant)
string text = "menu.help".Tr("UI").Short();
// Returns: "menu.help" content (fallback to base)
```

## Common Patterns

### Dynamic UI Density

```csharp
public class UIController : MonoBehaviour
{
    public enum UIDensity { Comfortable, Compact, Dense }
    
    public UIDensity density;
    
    string GetLocalizedText(string key)
    {
        var task = key.Tr("UI");
        
        return density switch
        {
            UIDensity.Dense => task.Abbr(),
            UIDensity.Compact => task.Short(),
            UIDensity.Comfortable => task.Long(),
            _ => task
        };
    }
}
```

### Accessibility Options

```csharp
void UpdateForAccessibility(bool verboseMode)
{
    foreach (var label in allLabels)
    {
        label.text = verboseMode 
            ? label.key.Tr("UI").Long()
            : label.key.Tr("UI").Short();
    }
}
```

### Notification Text

```csharp
void ShowNotification(string key, bool isFullscreen)
{
    string message = key.Tr("Notifications");
    
    if (!isFullscreen)
    {
        message = key.Tr("Notifications").Short();
    }
    
    notificationText.text = message;
}
```
