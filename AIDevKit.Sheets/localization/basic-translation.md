# Basic Text Translation

The `.Tr()` extension method is the core of the localization API.

## Basic Usage

```csharp
using Glitch9.AIDevKit.Sheets;

// Translate with table name
string title = "menu.title".Tr("UI");

// Use default table (if configured)
string message = "welcome.message".Tr();
```

## How It Works

The `.Tr()` method:
1. Creates a `TrStringTask` object
2. Looks up the key in the specified localization table
3. Returns the localized value for the current locale
4. Implicitly converts to `string`

## Key Structure

Keys use dot notation for organization:

