# Locale Override

You can override the locale for a specific translation without changing the global `CurrentLocale`.

## Using .Locale()

```csharp
// Override with Locale enum
string korean = "menu.title".Tr("UI").Locale(Locale.KoKR);
string japanese = "menu.title".Tr("UI").Locale(Locale.JaJP);

// Override with IETF tag string
string chinese = "menu.title".Tr("UI").Locale("zh-CN");
```

## Use Cases

### Multi-language Display

Show the same content in different languages simultaneously:

```csharp
public class LanguageComparisonUI : MonoBehaviour
{
    public TextMeshProUGUI englishText;
    public TextMeshProUGUI koreanText;
    public TextMeshProUGUI japaneseText;

    void UpdateText(string key)
    {
        englishText.text = key.Tr("UI").Locale(Locale.EnUS);
        koreanText.text = key.Tr("UI").Locale(Locale.KoKR);
        japaneseText.text = key.Tr("UI").Locale(Locale.JaJP);
    }
}
```

### Language Preview

Preview translations before applying:

```csharp
void PreviewLanguage(Locale locale)
{
    string preview = "menu.title".Tr("UI").Locale(locale);
    previewLabel.text = $"Preview: {preview}";
}
```

### Per-User Locale

In multiplayer, show each user's preferred language:

```csharp
string GetUserGreeting(User user)
{
    return "greeting.welcome"
        .Tr("UI")
        .Locale(user.PreferredLocale)
        .Format(user.Name);
}
```

## Global vs Override

```csharp
// Global locale (affects all translations)
Localization.CurrentLocale = Locale.KoKR;
string text1 = "menu.title".Tr("UI"); // Returns Korean

// Override (only affects this translation)
string text2 = "menu.title".Tr("UI").Locale(Locale.JaJP); // Returns Japanese

// Back to global
string text3 = "menu.subtitle".Tr("UI"); // Returns Korean again
```

## Chaining

Locale override can be chained with other methods:

```csharp
string text = "item.apple"
    .Tr("Items")
    .Locale(Locale.JaJP)
    .Count(5)
    .Child("name");
```

## IETF Language Tags

You can use standard IETF language tags:

```csharp
string text = "menu.title".Tr("UI").Locale("en-US");
string text = "menu.title".Tr("UI").Locale("ko-KR");
string text = "menu.title".Tr("UI").Locale("ja-JP");
string text = "menu.title".Tr("UI").Locale("zh-CN");
string text = "menu.title".Tr("UI").Locale("zh-TW");
```

## Performance

Locale override does NOT cache separately per locale. Each call resolves using the specified locale.

```csharp
// These create separate resolutions
for (int i = 0; i < 100; i++)
{
    string text = "menu.title".Tr("UI").Locale(Locale.JaJP);
    // Resolves 100 times
}

// Better: resolve once
string text = "menu.title".Tr("UI").Locale(Locale.JaJP);
for (int i = 0; i < 100; i++)
{
    // Use cached 'text' instead
}
```
