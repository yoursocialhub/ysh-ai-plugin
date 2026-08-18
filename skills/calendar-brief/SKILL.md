---
name: calendar-brief
description: Produce a fixed, one-screen status brief for a Your Social Hub content calendar. Use when the user asks what is waiting for review, sent back, rejected, failed, or approved without a date in a calendar.
---

# Content calendar brief

Produce a fixed-shape status brief for the calendar the user names. Ask them to choose only when the hub or calendar is ambiguous.

1. Resolve the calendar with `list_hubs` then `list_content_calendars`, paging calendars until the named one is found.
2. Call `list_content` for each relevant status, paging until all matching items are included.
3. Call `list_ideas` once.

Report exactly:

- **Needs a person:** counts and titles for `revisions_needed`, `rejected`, and `publication_failed`.
- **In review:** count.
- **Armed:** `approved` items with a `publicationDate`, including dates.
- **Approved, no date:** count and titles; these cannot publish.
- **Pipeline:** ideas by `idea`, `creation`, and `sent` status.

Do not drill into individual content, write anything, or add a second analysis. Hand off to `calendar-audit` for a fuller audit.
