# AIDevKit.Sheets - Localization API Reference

This document describes the localization API provided by **`Glitch9.AIDevKit.Sheets`**. Localization is a **subsystem of Sheets**, backed by spreadsheet tables and resolved through fluent `Tr*Task` objects.

***

## Core Entry Point: `Localization`

Namespace: `Glitch9.AIDevKit.Sheets`

### `Localization.Tables`

Read-only dictionary of registered localization tables.

```csharp
var tables = Localization.Tables;
```

***

### `Localization.CurrentLocale`

Gets/sets the current locale used by the Sheets localization subsystem.

```csharp
Localization.CurrentLocale = Locale.EnUS;
var locale = Localization.CurrentLocale;
```

***

### `Localization.IsInitialized`

Returns `true` once localization is initialized and ready to resolve values.

```csharp
if (Localization.IsInitialized)
{
    // Safe to use .Tr()
}
```

***

### `Localization.Initialize(params SheetTable[] preloadedTables)`

Initializes localization. You may optionally preload localization tables.

```csharp
Localization.Initialize(preloadedTables);
```

***

### `Localization.RegisterTable(SheetTable table)`

Registers a table into the appropriate manager (currently localization tables).

```csharp
Localization.RegisterTable(table);
```

***

### `Localization.UnregisterTable(TableCategory category, string tableName)`

Unregisters a table from the given category.

```csharp
Localization.UnregisterTable(TableCategory.Localization, "UI");
```

***

### `Localization.RegisterEventCallback<T>(Action<T> callback)`

### `Localization.UnregisterEventCallback<T>(Action<T> callback)`

Registers/unregisters callbacks for localization events.

```csharp
Localization.RegisterEventCallback<MyLocalizationEvent>(evt => { /* ... */ });
Localization.UnregisterEventCallback<MyLocalizationEvent>(evt => { /* ... */ });
```

***

## Fluent Localization Tasks

Localization is performed via `TrTask` objects. A `TrTask` resolves:

* `key`
* `table`
* optional `locale override`
* optional `child key`
* optional `fallback`

The resolution happens lazily when you access `.value` (or implicitly convert).

***

## `TrTask<TSelf, TCell, TValue>` (Base)

### `.value`

Resolves the localized value (fetches lazily, caches result).

```csharp
var task = "menu.title".Tr("UI");
string result = task.value;
```

***

### `.Locale(Locale locale)` / `.Locale(string ietfTag)`

Overrides locale for this task only.

```csharp
string s1 = "menu.title".Tr("UI").Locale(Locale.KoKR).value;
string s2 = "menu.title".Tr("UI").Locale("ko-KR").value;
```

***

### `.Child(string key)`

Appends a child key: `"item.apple".Child("desc")` → `"item.apple.desc"`

```csharp
string desc = "item.apple".Tr("Items").Child("desc").value;
```

***

### `.Fallback(TValue value)`

Fallback used when the key is missing or the resolved value is invalid.

```csharp
string title = "missing.key".Tr("UI").Fallback("Default Title").value;
```

***

## `.Tr()` Extension API (Entry Methods)

All defined in `TrTaskExtensions`.

### `string.Tr(string table = null) -> TrStringTask`

```csharp
string title = "menu.title".Tr("UI");
string defaultTable = "home.welcome".Tr();
```

> `TrStringTask` has an implicit conversion to `string`, so the examples above return a string.

***

### `string.TrVoiced(string table = null) -> TrVoicedStringTask`

For **text + voice(AudioClip)** stored in a voiced-string cell.

```csharp
var voiced = "npc.greeting".TrVoiced("Dialogues");

string text = voiced.text;          // localized text
AudioClip voice = voiced.voice;     // cached audio (null until loaded)
```

Loading audio:

```csharp
await voiced.LoadAsync();                 // returns AudioClip
voiced.Load(clip => audioSource.clip = clip); // callback + starts async load
```

***

### `string.TrAsset<T>(string table = null) -> TrAssetTask<T>`

Loads a Unity asset referenced by the localization table.

```csharp
var iconTask = "item.apple.icon".TrAsset<Sprite>("Items");
Sprite icon = await iconTask.LoadAsync();
```

Or callback style:

```csharp
"item.apple.icon"
    .TrAsset<Sprite>("Items")
    .Load(sprite => image.sprite = sprite);
```

***

### `enum.Tr() -> TrStringTask`

Localizes an enum value using the enum localization table mapping managed by Sheets.

```csharp
string job = JobType.Warrior.Tr(); // returns string (via TrStringTask)
```

***

### Deferred Resolution

Use these when localization may not be initialized yet.

#### `string.TrCallback(string table, Action<TrStringTask> callback)`

```csharp
"loading.message".TrCallback("UI", task =>
{
    label.text = task; // implicit string conversion
});
```

#### `string.TrDeferred(Action<TrStringTask> callback)`

Same as `TrCallback(key, null, callback)`.

```csharp
"loading.message".TrDeferred(task => label.text = task);
```

#### `enum.TrDeferred(Action<TrStringTask> callback)`

```csharp
JobType.Warrior.TrDeferred(task => label.text = task);
```

***

## `TrStringTask` (Text Localization + Variants)

`TrStringTask` supports fluent variants that modify the **final lookup key**. Variants are appended only if the candidate key exists in the target table.

### Fluent Options

#### `.Count(int count)` / `.C(int count)`

Pluralization variant is applied when `count != 1`.

```csharp
string s = "apple".Tr("Items").Count(3); // may append ".plural" if it exists
```

#### `.Gender(TextGender gender)` + shortcuts `.M() .F() .N()`

```csharp
string s = "friend".Tr("UI").Gender(TextGender.Feminine);
string s2 = "friend".Tr("UI").F();
```

#### `.TextFormat(TextFormat format)` + shortcuts `.Abbr() .Short() .Long()`

```csharp
string s = "time.remaining".Tr("UI").Short();
```

#### `.Format(params string[] args)`

Uses `string.Format(raw, args)` when resolving the value.

```csharp
string s = "welcome".Tr("UI").Format("Master");
```

> This is standard `string.Format`, not SmartFormat.

***

## Real-time AI Translation

### `string.TrRealtime() -> TrRealtimeTask`

This translates arbitrary input text using an AI translation service (runtime).

```csharp
string result = await "Hello".TrRealtime()
    .SetSourceLocale(Locale.EnUS)
    .SetTargetLocale(Locale.KoKR)
    .SetService(TrService.Google)   // or whatever is supported
    .SetModel(Model.YourModel)      // if applicable
    .TrAsync();
```

You can also register a callback:

```csharp
"Hello".TrRealtime()
    .SetTargetLocale(Locale.JaJP)
    .SetCallback(t => Debug.Log(t));
```

***

## Date/Time Localization

### `DayOfWeek.Tr() -> string`

```csharp
string dow = DayOfWeek.Monday.Tr();
```

***

### `DateTime.Tr(TimeFormat format = TimeFormat.DateTime) -> string`

```csharp
string s = DateTime.Now.Tr(TimeFormat.FullDate);
```

Locale override:

```csharp
string s = DateTime.Now.Tr(Locale.KoKR, TimeFormat.FullDate);
```

***

### `TimeSpan.Tr() -> string`

`TimeSpan.Tr()` currently returns a string by internally using `TrTimeSpanTask`.

```csharp
string s = TimeSpan.FromMinutes(90).Tr();
```

If you need custom formatting/range control, use `TrTimeSpanTask` directly:

```csharp
var task = new TrTimeSpanTask(TimeSpan.FromMinutes(90))
    .InMinutes()
    .ToHours()
    .Short()
    .AddSpace();

string s = task.ToString();
```

Available options include:

* `.ShowZeroValues()`
* `.AddSpace()`
* `.TextFormat(TextFormat)` / `.Short()` / `.Abbreviated()`
* `.InSeconds() / InMinutes() / InHours() / InDays() / InWeeks() / InMonths() / InYears()`
* `.ToSeconds() / ToMinutes() / ToHours() / ToDays() / ToWeeks() / ToMonths()`

***
