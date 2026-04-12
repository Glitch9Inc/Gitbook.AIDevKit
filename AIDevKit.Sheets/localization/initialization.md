# Initialization

## Basic Initialization

Before using localization features, you must initialize the system:

```csharp
using Glitch9.AIDevKit.Sheets;

void Start()
{
    Localization.Initialize();
}
```

## Preloading Tables

You can preload specific localization tables during initialization:

```csharp
// Load tables manually
var uiTable = SheetDatabase.Load<LocalizationTable>("UI");
var itemsTable = SheetDatabase.Load<LocalizationTable>("Items");

// Initialize with preloaded tables
Localization.Initialize(uiTable, itemsTable);
```

## Setting Current Locale

Set the active locale for your game:

```csharp
// Set locale
Localization.CurrentLocale = Locale.EnUS;

// Or from string
Localization.CurrentLocale = Locale.FromIetfTag("ko-KR");
```

### Available Locales

```csharp
Locale.EnUS   // English (United States)
Locale.KoKR   // Korean (Korea)
Locale.JaJP   // Japanese (Japan)
Locale.ZhCN   // Chinese (Simplified)
Locale.ZhTW   // Chinese (Traditional)
Locale.EsES   // Spanish (Spain)
Locale.FrFR   // French (France)
Locale.DeDE   // German (Germany)
// ... and more
```

## Checking Initialization Status

```csharp
if (Localization.IsInitialized)
{
    // Safe to use .Tr() and other localization APIs
    string text = "menu.title".Tr("UI");
}
else
{
    Debug.LogWarning("Localization not initialized yet");
}
```

## Complete Initialization Example

```csharp
using Glitch9.AIDevKit.Sheets;
using UnityEngine;

public class GameInitializer : MonoBehaviour
{
    void Awake()
    {
        // Preload all localization tables
        var uiTable = SheetDatabase.Load<LocalizationTable>("UI");
        var dialogueTable = SheetDatabase.Load<LocalizationTable>("Dialogues");
        var itemsTable = SheetDatabase.Load<LocalizationTable>("Items");

        // Initialize localization
        Localization.Initialize(uiTable, dialogueTable, itemsTable);

        // Set default locale (could be from player settings)
        Localization.CurrentLocale = GetPlayerLocale();

        Debug.Log("Localization initialized");
    }

    Locale GetPlayerLocale()
    {
        // Load from PlayerPrefs or use system language
        string saved = PlayerPrefs.GetString("Locale", "");
        
        if (!string.IsNullOrEmpty(saved))
        {
            return Locale.FromIetfTag(saved);
        }

        return Locale.EnUS; // Default
    }
}
```

## Runtime Locale Changes

You can change locale at runtime:

```csharp
public void ChangeLanguage(Locale newLocale)
{
    Localization.CurrentLocale = newLocale;
    
    // Refresh all UI text
    RefreshAllLocalizedText();
}
```

## Best Practices

1. **Initialize early**: Call `Initialize()` in `Awake()` or at game startup
2. **Preload tables**: Preload frequently used tables for better performance
3. **Check initialization**: Always check `IsInitialized` before using localization in dynamic scenarios
4. **Cache locale**: Save player's locale preference to PlayerPrefs
