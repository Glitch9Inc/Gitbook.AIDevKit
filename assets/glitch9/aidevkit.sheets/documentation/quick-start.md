# Smart Localization - QuickStart Guide

This guide will walk you through setting up and using the `SmartLocalization` system to localize your Unity project efficiently with support for strings, sprites, audio, and more.

***

## 1. Requirements

* Unity with `Addressables` (optional but recommended)
* \[UniTask] (https://glitch9.gitbook.io/docs/support/how-to-setup-unitask)
* \[Newtonsoft.Json] (https://glitch9.gitbook.io/docs/support/how-to-setup-newtonsoft.json)

***

## 2. Initialization

### Automatic Initialization

If you’ve enabled:

```csharp
LocalizationSettings.AutoInitialization = true;
```

Then SmartLocalization initializes on first use.

### Manual Initialization

```csharp
await SmartLocalization.Initialize();
```

***

## 3. Setting the Locale

```csharp
SmartLocalization.locale = Locale.Korean;
```

You can also retrieve the current locale:

```csharp
var current = SmartLocalization.locale;
```

***

## 4. Loading Localization Data (Addressables)

If using Addressables, the system automatically loads and maps the tables:

```csharp
await SmartLocalization.Initialize();
```

Under the hood:

* `LoadL10nAddressableTablesAsync()`: loads `LocalizationTable`s
* `LoadL10nAddressableAssetsAsync()`: loads associated assets

***

## 5. Localizing Texts

Use the fluent `Tr()` system to localize keys:

```csharp
string title = "menu.title".Tr();
```

Or in context:

```csharp
string weaponName = myWeaponType.Tr();
```

> Enums must have `[LocalizedEnum("tableName")]` attribute.

***

## 6. Delayed Translation (Callback)

When the system isn't ready:

```csharp
"menu.start".TrCallback((tr) =>
{
    uiText.text = tr;
});
```

***

## 7. Using TrTask Fluent API

TrTask provides a rich chaining API:

```csharp
"apple".Tr()
    .Count(3)
    .Gender(TextGender.Feminine)
    .Short()
    .Format("red")
    .ToString();
```

Supports fallback:

```csharp
"unknown.key".Tr().FallBack("Default Text");
```

***

## 8. Localizing Assets

```csharp
Sprite icon = "item.apple.icon".Tr().sprite;
AudioClip voice = "npc.greeting".Tr().voice;
```

Or fallback:

```csharp
var texture = "bg.image".Tr().FallBack(defaultTexture).texture;
```

***

## 9. Localizing Dates & TimeSpans

```csharp
DateTime now = DateTime.Now;
string formatted = now.Tr(TimeFormat.FullDate);
```

TimeSpan example:

```csharp
var duration = TimeSpan.FromMinutes(90);
string readable = duration.Tr().InMinutes().ToHours().Abbr().ToString();
```

***

## You're Ready!

SmartLocalization brings structured, scalable, and extensible localization support to your Unity project. ✨\
Try using `Tr()` for fluent and flexible localizations across all asset types.
