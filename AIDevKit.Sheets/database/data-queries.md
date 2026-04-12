# Data Queries

## Accessing Row Data

### Get Row by Key

```csharp
var itemTable = SheetDatabase.Load<ItemTable>("Items");

// Get row by key
var appleRow = itemTable.GetRow("apple");
```

### Check if Row Exists

```csharp
if (itemTable.ContainsRow("sword"))
{
    var swordRow = itemTable.GetRow("sword");
}
```

## Reading Cell Values

### Basic Types

```csharp
var row = itemTable.GetRow("apple");

string name = row.GetString("Name_EN");
int price = row.GetInt("Price");
float weight = row.GetFloat("Weight");
bool stackable = row.GetBool("Stackable");
```

### With Default Values

```csharp
// Returns default value if cell is missing or invalid
string description = row.GetString("Description", "No description");
int maxStack = row.GetInt("MaxStack", 99);
```

## Enum Values

```csharp
// Assuming ItemType is an enum
ItemType type = row.GetEnum<ItemType>("Type");

// With default
ItemType rarity = row.GetEnum("Rarity", ItemType.Common);
```

## Iterating Rows

### Get All Rows

```csharp
var allRows = itemTable.GetRows();

foreach (var row in allRows)
{
    string key = row.Key;
    string name = row.GetString("Name_EN");
    Debug.Log($"{key}: {name}");
}
```

### Filter Rows

```csharp
var weapons = itemTable.GetRows()
    .Where(row => row.GetEnum<ItemType>("Type") == ItemType.Weapon);

foreach (var weapon in weapons)
{
    Debug.Log(weapon.GetString("Name_EN"));
}
```

## Column Information

### Get Column Names

```csharp
var columns = itemTable.GetColumnNames();

foreach (string columnName in columns)
{
    Debug.Log($"Column: {columnName}");
}
```

### Check Column Exists

```csharp
if (itemTable.HasColumn("Rarity"))
{
    // Column exists
}
```

## Advanced Queries

### LINQ Queries

```csharp
// Get expensive items
var expensiveItems = itemTable.GetRows()
    .Where(row => row.GetInt("Price") > 1000)
    .OrderByDescending(row => row.GetInt("Price"))
    .Take(10);

// Group by type
var itemsByType = itemTable.GetRows()
    .GroupBy(row => row.GetEnum<ItemType>("Type"));

foreach (var group in itemsByType)
{
    Debug.Log($"{group.Key}: {group.Count()} items");
}
```

### Custom Queries

```csharp
public static class TableExtensions
{
    public static IEnumerable<SheetRow> FindByType(
        this ItemTable table, 
        ItemType type)
    {
        return table.GetRows()
            .Where(row => row.GetEnum<ItemType>("Type") == type);
    }
}

// Usage
var weapons = itemTable.FindByType(ItemType.Weapon);
```
