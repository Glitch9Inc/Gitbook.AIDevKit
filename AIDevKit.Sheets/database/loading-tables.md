# Loading DB Tables

## SheetDatabase API

The `SheetDatabase` class provides static methods to load database tables.

### Load Table

```csharp
using Glitch9.AIDevKit.Sheets;

// Load table by name
var itemTable = SheetDatabase.Load<ItemTable>("Items");
```

### Table Types

Tables are stored as ScriptableObject types. Each table must inherit from `SheetTable`:

```csharp
public class ItemTable : SheetTable
{
    // Table implementation
}
```

## Loading from Resources

Tables are loaded from the Resources folder:

