---
description: Compress long chats into concise summaries for efficient memory
---

# Conversation Summary

Conversation summaries condense prior messages into short structured context.

They help maintain continuity when the original conversation is too long to keep in the active context window.

## Benefits

- Reduces token usage compared to replaying full history
- Preserves key decisions, preferences, and facts
- Improves response consistency in long-running chats

## Suggested strategy

- Generate/update summaries periodically (not every single turn)
- Keep summaries factual and compact
- Refresh summaries after major topic shifts
