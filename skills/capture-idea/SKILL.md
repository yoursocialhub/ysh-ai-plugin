---
name: capture-idea
description: Capture one user-described idea as a single entry in the Your Social Hub Ideas Hub. Use when the user asks to save a quick content idea, thought, hook, or note without a full drafting workflow.
---

# Capture an idea

Capture the user's current one-line idea as one Ideas Hub entry. Do not do voice research or create a batch.

1. Resolve the calendar with `list_hubs` then `list_content_calendars`. Ask only when more than one calendar is plausible, and retain the user's choice for this conversation. Read the selected calendar's `formats` before creating the idea.
2. Turn the idea into a `title` and `notes`. Include a `caption` only when the user supplied actual copy. Set `type` to the requested kind (`post`, `carousel`, `story`, `thread`, or `tiktok`); default to `post`. In particular, a carousel request requires `type: 'carousel'`, not `type: 'post'` with the word "carousel" in the title or notes.
3. If the user names a format and the selected calendar contains a matching format, pass that existing format's id as `formatId`. A carousel request should use the calendar's `Carousel` format when present. Never invent a format id. If the format is unavailable, still set the correct `type` and tell the user that no calendar format was assigned.
4. Call `create_idea` once. Do not send `fileOrder`. Set `publicationDate` only if the user supplied a date, formatted as full ISO-8601 UTC. Leave `socialAccountIds` unset unless the user named or selected accounts.
5. Check the returned idea's `type`, `formatId`, `publicationDate`, and `socialAccountIds` against the request before reporting success. If the requested format was omitted and `update_idea` is available, set the existing calendar format id with `update_idea`, omitting `fileOrder`, and verify the returned idea. If `type` is wrong, do not claim that the requested kind was created; report the mismatch because `update_idea` cannot change `type`.
6. When `render_social_preview` is available, call it with `entityType: 'idea'` and the newly created id so the user can inspect the idea in its platform frame.
7. Report the created idea ID and title in one line. Say that the idea is planning-only until it is promoted to content for review.

If `create_idea` is absent, explain that `ideas:write` was not granted and ask the user to reconnect with that permission.
