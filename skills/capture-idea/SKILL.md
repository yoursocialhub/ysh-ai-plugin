---
name: capture-idea
description: Capture one user-described idea as a single entry in the Your Social Hub Ideas Hub. Use when the user asks to save a quick content idea, thought, hook, or note without a full drafting workflow.
---

# Capture an idea

Capture the user's current one-line idea as one Ideas Hub entry. Do not do voice research or create a batch.

1. Resolve the calendar with `list_hubs` then `list_content_calendars`. Ask only when more than one calendar is plausible, and retain the user's choice for this conversation.
2. Turn the idea into a `title` and `notes`. Include a `caption` only when the user supplied actual copy. Infer `type` from the wording (`post`, `carousel`, `story`, `thread`, or `tiktok`); default to `post`.
3. Call `create_idea` once. Do not send `fileOrder`. Set `publicationDate` only if the user supplied a date, formatted as full ISO-8601 UTC.
4. When `render_social_preview` is available, call it with `entityType: 'idea'` and the newly created id so the user can inspect the idea in its platform frame.
5. Report the created idea ID and title in one line. Say that the idea is planning-only until it is promoted to content for review.

If `create_idea` is absent, explain that `ideas:write` was not granted and ask the user to reconnect with that permission.
