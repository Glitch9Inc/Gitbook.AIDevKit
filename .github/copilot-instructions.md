# AIDevKit Unity Project - AI Coding Agent Instructions

## Project Overview

**AIDevKit** is a modular Unity 6 editor toolkit for AI-powered development, featuring spreadsheet/localization management, pixel art tools, and extensible core libraries. Built with UIToolkit and organized into distinct assembly definitions for clean separation of concerns.

## Architecture

### Modular Assembly Structure

* **Core**: `Glitch9.AIDevKit` - Runtime foundation with AI integration
* **Editor Tools**: `Glitch9.AIDevKit.Editor.*` - Editor-specific implementations
* **Specialized Modules**: `.Sheets`, `.PixelArt`, `.Pro`, `.Research` - Feature domains
* **Foundation**: `Glitch9.CoreLib.*` - Reusable utilities (IO, networking, auth, vector store)
* **Compatibility**: `Glitch9.Compat` - Legacy/DLL-excluded code requiring defining symbols

Each module follows the pattern: `Runtime/`, `Editor/`, `Samples/` with corresponding `.asmdef` files.

### Event-Driven UI Pattern (Spreadsheet System)

The spreadsheet editor uses a **Command/Event architecture** with reactive UIElements:

```csharp
// Commands: Mutate state (undoable, recorded)
AISheetEditor.DispatchCommand(new AddNewRowEntryAtEnd(count, tableEntry));

// Events: Notify UI of changes (read-only propagation)
AISheetEditor.DispatchEvent(new ColumnWidthChanged(columnId, index, newWidth));
```

**Key Classes**:

* `SpreadsheetCommand` - Base for all state mutations
* `SpreadsheetEvent` - Base for UI notifications
* `ICommandHandler<T>` / `IEventHandler<T>` - Handler interfaces
* Event handlers react to events via pattern matching (`if (evt is ColumnWidthChanged changed)`)

### View Component Hierarchy

```
SpreadsheetWindow (Editor Window)
├── SpreadsheetTools (Toolbar with factory methods)
├── SpreadsheetHeader (Column headers)
├── SpreadsheetListView (Data rows)
│   └── RowView (Individual row with CellManager)
│       ├── IndexCellView (Static: row number)
│       ├── KeyCellView (Static: key column)
│       └── DataCellView / LocalizationCellView (Dynamic columns)
└── SpreadsheetSideBar (Generation/Agent)
```

**Cell View Polymorphism**: `CellView.Create()` factory returns specialized types based on column index and table category.

## Coding Conventions

### Naming Patterns

* **Private fields**: `m_VariableName` (Hungarian 'm\_' prefix)
* **Static classes**: Utility/config classes with `Config`, `Factory`, `Utility` suffixes
  * Example: `SpreadsheetConfig`, `SpreadsheetToolsFactory`, `CellFieldFactory`
* **Const containers**: Nested `static class` for constants (e.g., `SpreadsheetPrefs.Keys`)
* **Comments**: Write all new comments in English. Preserve existing Korean comments when editing surrounding code without rewriting it.

### UI Toolkit Patterns

* **Factory methods**: UI construction split into `*Factory` classes for readability
  * `SpreadsheetToolsFactory.CreateAddButton()` instead of inline construction
* **USS class management**: `AddToClassList()` / `EnableInClassList()` for state-based styling
* **Event registration**: `RegisterValueChangedCallback()` with lambda handlers

### State Management

* **EPrefs**: Editor preferences wrapper (`EPrefs<T>` for type-safe EditorPrefs)
* **Entry pattern**: Data wrappers ending in `Entry` (e.g., `ColumnEntry`, `RowEntry`, `TableEntry`)
  * Entries encapsulate metadata and dispatch events on property changes
* **Selection state**: Centralized in `SpreadsheetSelection` static class

## Key Integration Points

### Dependencies

* **UniTask**: Async operations (`using Cysharp.Threading.Tasks`)
* **Newtonsoft.Json**: Serialization (define: `UNITY_NEWTONSOFT_JSON`)
* **Unity UIToolkit**: All editor UI (no IMGUI)

### External Systems

* **Vector Store**: `Glitch9.VectorStore` - Document chunking/search
* **OAuth**: `Glitch9.IO.Networking.Auth` - Authentication flows (loopback, PKCE)
* **RSS Reader**: `Glitch9.IO.Networking.RssReader` - Feed parsing

## Common Tasks

### Adding a New Spreadsheet Command

1. Define command class inheriting `SpreadsheetCommand`
2. Implement handler in `SpreadsheetCommandHandler.HandleCommand()`
3. Dispatch via `AISheetEditor.DispatchCommand(new MyCommand())`

### Creating a Custom Cell Type

1. Inherit from `CellViewBase` or `DataCellView`
2. Override `CreateContextMenuBuilder()` for right-click menu
3. Override `CreateKeyInputEventHandler()` for keyboard behavior
4. Add factory logic to `CellView.Create()` switch

### Building a UIToolkit Editor Window

* Extend `UxmlEditorWindow` (custom base in CoreLib)
* Apply styles via `this.ApplyStyleSheetWithMarker()`
* Use `VisualElement.AddToClassList()` for styling hooks
* Follow existing factory patterns for complex toolbars

### Creating a SpreadsheetAgent Tool

SpreadsheetAgent tools are AI-callable functions that operate on spreadsheet data. Follow this pattern:

**1. Tool Class Structure**

```csharp
[SpreadsheetAgentTool("tool_name", "Category", "Brief description")]
sealed class ToolName : EditorFunctionTool
{
    public override string Name => "tool_name";
    
    public override Function Create()
    {
        return Function.Create<ToolArgs>(
            name: Name,
            description: "Detailed description for AI understanding"
        );
    }
    
    public override async UniTask<FunctionOutput> ExecuteAsync(FunctionCall call)
    {
        if (call == null)
            return FunctionOutput.Error(null, "FunctionCall is null");
            
        await UniTask.Yield();
        
        // Parse arguments
        ToolArgs args;
        try
        {
            if (string.IsNullOrEmpty(call.Arguments))
                return FunctionOutput.Error(call, "Arguments are required");
            args = JsonNet.Deserialize<ToolArgs>(call.Arguments);
        }
        catch (Exception ex)
        {
            return FunctionOutput.Error(call, $"Invalid arguments: {ex.Message}");
        }
        
        // Validate arguments
        // ... validation code ...
        
        try
        {
            // Get target table (auto-uses current table if not specified)
            TableEntry table = SpreadsheetAgentToolUtility.GetTargetTable(
                args.TableName, args.Category);
            if (table == null)
                return FunctionOutput.Error(call, "Table not found or no active table");
            
            // Execute tool logic
            // ... implementation ...
            
            return FunctionOutput.Success(call, new ToolResult { /* ... */ });
        }
        catch (Exception ex)
        {
            return FunctionOutput.Error(call, $"Failed: {ex.Message}");
        }
    }
}
```

**2. Arguments Class with Attributes**

```csharp
[FunctionSchema("tool_name", "Brief description", Strict = true)]
private class ToolArgs
{
    [FunctionProperty("table_name", "Target table name (optional, uses active table if omitted)")]
    public string TableName { get; set; }
    
    [FunctionProperty("category", "Table category: 'Database', 'Localization', or 'Dialogue' (optional)")]
    public string Category { get; set; }
    
    [FunctionProperty("param_name", "Parameter description", Required = true)]
    public string ParamName { get; set; }
}
```

**3. Result Class**

```csharp
[Serializable]
private class ToolResult
{
    public string table_name;
    public string result_field;
    // Use snake_case for JSON serialization
}
```

**Key Guidelines**:

* **Category**: "Common", "Database", "Localization" - determines tool grouping
* **Current Table Context**: Always use `SpreadsheetAgentToolUtility.GetTargetTable()` - automatically resolves to `SpreadsheetHub.CurrentTableEntry` if table\_name is null/empty
* **Attributes**: Use `[FunctionSchema]` on args class, `[FunctionProperty]` on each property
* **Access Modifiers**: Args/Result classes should be `private` (not `internal sealed`)
* **Async Pattern**: Always `await UniTask.Yield()` for async behavior
* **Error Handling**: Use `FunctionOutput.Error(call, message)` for validation/execution errors
* **Safety Locks**: For destructive operations, require `confirm: true` parameter
* **Entry Pattern**: Access data via `TableEntry`, `RowEntry`, `ColumnEntry`, `CellEntry`
* **Comments**: Write all internal documentation and inline comments in English.

**Common Entry Methods**:

* `table.FindColumnEntryByName(name)` / `table.FindColumnEntryByIndex(index)`
* `table.FindRowEntryById(id)` / `table.FindRowEntryByKey(key)`
* `row.GetOrCreateCellEntry(columnId, dataType)`
* `cellEntry.UpdateCellValue(SheetCell)`

**Tool Discovery**: Tools are auto-discovered via `EditorAgentUtility.DiscoverEditorAgentTools<SpreadsheetAgentToolAttribute>()` and registered in `SpreadsheetAgentController`

## File Organization

```
Assets/Glitch9/
├── AIDevKit/                    # Main toolkit
│   ├── Runtime/Core/            # Core runtime scripts
│   ├── Editor/Core/             # Core editor tools
│   └── Samples/                 # Example scenes/prefabs
├── AIDevKit.Sheets/             # Spreadsheet system
│   ├── Runtime/                 # Data models
│   └── Editor/Scripts/          # Full editor implementation
│       ├── Spreadsheet/         # Main spreadsheet logic
│       │   ├── Window/          # UI components (numbered)
│       │   ├── Elements/        # Custom VisualElements
│       │   ├── Messages/        # Commands & Events
│       │   └── System/          # Core systems (undo, clipboard)
│       └── UI/Windows/          # Popup/dialog windows
└── CoreLib/                     # Shared utilities
    ├── CoreLib/                 # Base library
    ├── CoreLib.IO/              # File/network operations
    └── CoreLib.Editor.*/        # Editor utilities
```

## Testing & Debugging

* Unity Editor required (Unity 6+)
* Use `SseLog` logger class (custom logging utility)
* Editor windows: `Window > AI Dev Kit > [Feature]` menu paths
* Check `.DotSettings` files for ReSharper/Rider conventions

***

## Language Rules

* **All code comments must be written in English.**\
  This applies to inline comments, XML doc comments, and any other in-code documentation.
* **Changelogs must always be written in English**, regardless of the language used in the conversation.
* **When generating changelogs, follow a structured format** similar to the example below:
  * Use a **version title header**: `# Major Update <version> (<date>)`
  * Organize entries using **section headers** such as `### Agent`, `### Settings`, etc.
  * Each entry must begin with a **tag**: `[New]`, `[Changed]`, `[Removed]`, `[Fixed]`, `[Improved]`, etc.
  * Reference **classes, interfaces, methods, or files** using backticks.
  * Provide a **clear and concise explanation** of the change.
  * Prefer **bullet lists** for readability.
  * Avoid unnecessary narrative text.
* Do **not** write Korean comments in new or edited code.\
  If existing Korean comments are encountered, leave them as-is unless the surrounding code is being rewritten.
* All other AI responses (explanations, summaries, answers) may be written in Korean or English depending on the conversation language.

***

## Critical Rule: No Guessing — Verify by Inspecting Files

When I ask for implementation details (names, signatures, enum members, paths, symbols, etc.), **do not fill gaps by guessing**.

### What you must do

* **Explicitly inspect the project files** before answering.
* If a file is needed, ask me to **provide the file or a link/path**, or request that I **upload it**.
* Prefer answers backed by **exact code references** from the inspected file(s).

### Absolutely forbidden

* **Inventing** enum members, class names, method names, event names, file names, or namespaces.
* “Likely/Probably” values that are not verified.
* Suggesting APIs that do not exist in this repo.

### Examples of forbidden mistakes

* Referencing a non-existent `enum` value.
* Writing `SpreadsheetCommandHandler.HandleCommand()` if the method is actually named differently.
* Using a guessed class like `AISheetEditorContext` if it does not exist.

### If information is missing

* Say: **"I need to check the file to confirm."**
* Then: request the minimal required file(s) or direct me to the exact location to inspect.

This rule overrides convenience: **accuracy over speed, always.**
