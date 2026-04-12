# AIDevKit.Sheets

**AIDevKit.Sheets** is a powerful spreadsheet and localization management system built for Unity 6.

## Features

### 📊 Spreadsheet Editor
- Modern UIToolkit-based editor interface
- CSV/JSON import/export support
- Real-time data editing and validation
- Complete Undo/Redo system

### 🌍 Localization System
- Fluent API-based translation system (`.Tr()`)
- Multi-language voice support (`.TrVoiced()`)
- Asset localization (`.TrAsset<T>()`)
- AI-powered real-time translation (`.TrRealtime()`)
- Pluralization, gender, and format variants

### 🗄️ Database Tables
- ScriptableObject-based data tables
- Runtime data query API
- Type-safe data access

## Quick Start

```csharp
// Initialize localization
Localization.Initialize();

// Basic translation
string text = "menu.title".Tr("UI");

// Voiced text
var voiced = "npc.greeting".TrVoiced("Dialogues");
string text = voiced.text;
AudioClip voice = await voiced.LoadAsync();

// Load database
var itemTable = SheetDatabase.Load<ItemTable>("Items");
var item = itemTable.GetRow("apple");
```

## Requirements

- Unity 6.0 or higher
- UIToolkit
- Newtonsoft.Json (optional)
- UniTask (for async operations)

## Documentation

- **Getting Started**: Installation and basic concepts
- **Spreadsheet Editor**: Editor usage guide
- **Database**: Runtime data access
- **Localization**: Localization API reference