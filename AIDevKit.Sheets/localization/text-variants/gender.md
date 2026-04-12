# Gender (.Gender)

The `.Gender()` method and shortcuts apply gender-specific text variants.

## Syntax

```csharp
// Full method
string result = key.Tr(table).Gender(TextGender.Masculine);

// Shortcuts
string result = key.Tr(table).M();  // Masculine
string result = key.Tr(table).F();  // Feminine
string result = key.Tr(table).N();  // Neuter
```

## TextGender Enum

```csharp
public enum TextGender
{
    Masculine,
    Feminine,
    Neuter
}
```

## How It Works

The system appends the appropriate suffix:

```csharp
.Gender(TextGender.Masculine) → .masculine
.Gender(TextGender.Feminine)  → .feminine
.Gender(TextGender.Neuter)    → .neuter
```

## Table Setup

| Key | English |
|-----|---------|
| class.warrior | Warrior |
| class.warrior.masculine | Warrior (Male) |
| class.warrior.feminine | Warrior (Female) |
| friend | Friend |
| friend.masculine | Male Friend |
| friend.feminine | Female Friend |

## Examples

### Character Class Display

```csharp
public class CharacterDisplay : MonoBehaviour
{
    public void ShowClass(CharacterClass charClass, bool isMale)
    {
        TextGender gender = isMale 
            ? TextGender.Masculine 
            : TextGender.Feminine;
        
        string className = $"class.{charClass}"
            .Tr("UI")
            .Gender(gender);
        
        classLabel.text = className;
    }
}
```

### NPC Dialogue

```csharp
void ShowGreeting(NPC npc)
{
    string greeting = "npc.greeting"
        .Tr("Dialogues")
        .Gender(npc.Gender);
    
    dialogueText.text = greeting;
}
```

### Achievement Titles

```csharp
void UnlockAchievement(string achievementKey, TextGender playerGender)
{
    string title = $"achievement.{achievementKey}"
        .Tr("Achievements")
        .Gender(playerGender);
    
    ShowNotification($"Unlocked: {title}");
}
```

## Language-Specific Usage

### Romance Languages

Gender is crucial for languages like French, Spanish, Italian:

