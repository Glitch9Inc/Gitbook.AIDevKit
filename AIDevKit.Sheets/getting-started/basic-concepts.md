# Basic Concepts

## Table Categories

AIDevKit.Sheets uses two main table categories:

### Database Tables
- Store game data (items, enemies, quests, etc.)
- Accessed via `SheetDatabase`
- ScriptableObject-based
- Type-safe row access

### Localization Tables
- Store translations and localized content
- Accessed via fluent `.Tr()` API
- Multi-locale support
- Automatic fallback handling

## Data Structure

### Spreadsheet Structure

