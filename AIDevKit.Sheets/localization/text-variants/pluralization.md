# Pluralization (.Count)

The `.Count()` method appends `.plural` suffix when the count is not equal to 1.

## Syntax

```csharp
string result = key.Tr(table).Count(count);
// Or shorthand:
string result = key.Tr(table).C(count);
```

## How It Works

```csharp
// When count == 1: uses base key
string text = "item.apple".Tr("Items").Count(1);
// Looks up: "item.apple"

// When count != 1: tries .plural variant
string text = "item.apple".Tr("Items").Count(3);
// Looks up: "item.apple.plural" (if exists), else "item.apple"
```

## Table Setup

In your localization spreadsheet:

| Key | English |
|-----|---------|
| item.apple | Apple |
| item.apple.plural | Apples |
| score | Point |
| score.plural | Points |

## Examples

### Basic Pluralization

```csharp
void UpdateInventory(int appleCount)
{
    string text = "item.apple"
        .Tr("Items")
        .Count(appleCount);
    
    label.text = $"{appleCount} {text}";
    // 1 Apple
    // 5 Apples
}
```

### With String Formatting

```csharp
// Table: "you.have.items" = "You have {0} {1}"
string message = "you.have.items"
    .Tr("UI")
    .Format(count.ToString(), "item.apple".Tr("Items").Count(count));

// Result: "You have 5 Apples"
```

### Currency Display

```csharp
void ShowGold(int amount)
{
    string unit = "currency.gold".Tr("UI").Count(amount);
    goldLabel.text = $"{amount} {unit}";
}
```

## Zero Handling

Count of 0 uses the plural form:

```csharp
string text = "item.apple".Tr("Items").Count(0);
// Returns plural: "Apples"
```

## Languages with Complex Pluralization

For languages with multiple plural forms (e.g., Russian, Polish), you may need additional keys:

