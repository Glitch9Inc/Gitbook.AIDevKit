---
icon: clock-rotate-left
---

# Generation History

Generation History is a built-in editor window that tracks every AI request made within the Unity Editor. All requests — text generation, image generation, speech, and more — are automatically recorded and persisted to a local JSON file.

## Opening the Window

Go to **Tools > AI Dev Kit > Generation History** in the Unity menu bar.

## Features

### Record List

All records are displayed in a sortable tree view with the following columns:

| Column | Description |
|---|---|
| ★ | Mark a record as a favorite |
| Date | Timestamp of the request |
| Model | AI model used |
| Request | Endpoint/request type (e.g. Chat, Image, TTS) |
| Input | Prompt or input text |
| Output | Generated response or result |
| Cost | Estimated token cost |
| Sender | Agent or component that initiated the request |

### Toolbar

- **File** menu — Save a snapshot of the current history, load a previous snapshot, or clear all records.
- **Search** — Filter records by keyword.
- **Favorites filter** — Show only starred records.
- **Archive filter** — Show only archived records.
- **Total cost** — Displays the cumulative estimated cost of all visible records. Click to recalculate.

### Snapshots

Snapshots are point-in-time backups of the full history file. A snapshot is automatically created before any destructive operation (e.g. "Clear All Records").

- Saved as `ai_request_history_snapshot_<timestamp>.json` inside the AIDevKit data folder.
- Can be listed and restored via **File > Load Snapshot**.

### Context Menu

Right-clicking a record exposes:

- **Copy Prompt** — Copies the input prompt to the clipboard.
- **Copy Response** — Copies the generated output to the clipboard.
- **Delete** — Removes the selected record(s) from history.

## Storage

Records are stored locally at:

```
<Application.persistentDataPath>/Glitch9/AIDevKit/ai_request_history.json
```

Legacy files (`prompt_history.json`) are automatically migrated to the new format on first load.

