---
---

# Data Manager

Data Manager is a unified editor window for browsing and managing AI-related data stored on remote provider servers or locally within the project. It provides three tabs, each backed by a sortable tree view.

## Opening the Window

Go to **Tools > AI Dev Kit > Data Manager** in the Unity menu bar.

## Tabs

### Uploaded Files

Lists all files uploaded to AI provider servers (e.g. OpenAI).

| Column | Description |
|---|---|
| API | The provider the file belongs to |
| File Name | File identifier / name |
| Size | File size in B / KB / MB |
| MIME Type | File content type |
| Created At | Upload timestamp |
| Expires At | Expiry timestamp (if applicable) |

The bottom bar shows per-provider total storage usage. Use the **Upload** button per provider to add new files, or right-click a row to **Delete** a file from the server.

### Conversations

Lists all saved agent conversations.

| Column | Description |
|---|---|
| Store | Storage backend (e.g. Local, Remote) |
| Title | Conversation name |
| Agent | Name of the agent that owns the conversation |
| Messages | Total message count |
| Last Message | Most recent user message preview |
| Created At | Creation timestamp |
| Updated At | Last update timestamp |

Right-click a conversation to **Delete** it. Clicking a row opens a detail window showing full metadata and a summary.

### Assistants

Lists OpenAI Assistants associated with your API key.

| Column | Description |
|---|---|
| Model | Base model the assistant uses |
| Name | Assistant name |
| Description | Short description |
| Response Format | Output format (e.g. text, json_object) |
| Created At | Creation timestamp |

Right-click an assistant to **Update** or **Delete** it from the OpenAI platform.

## Toolbar

- **Tab buttons** (Uploaded Files / Conversations / Assistants) — each tab also has a dropdown menu with tab-specific actions.
- **Search** — Filters the active tab's tree view by keyword.
- **Record counter** — Shows visible vs. total item count.
- **Refresh** — Reloads data for the active tab from the server or cache.

