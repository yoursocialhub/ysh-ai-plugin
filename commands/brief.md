---
description: One-screen status brief for a Your Social Hub content calendar — what is waiting for review, sent back, rejected, failed, or approved with no date.
argument-hint: [calendar name]
disable-model-invocation: true
---

Produce a fixed-shape status brief for the content calendar named in `$ARGUMENTS` (ask which one if it is empty or ambiguous).

1. `list_hubs` → `list_content_calendars(hubId)`, paged to `total`, to resolve the name to an id.
2. `list_content(contentCalendarId, status: …)` per status, paged to `total`.
3. `list_ideas(contentCalendarId)`.

Then report exactly this, and nothing more:

- **Needs a person:** counts and titles for `revisions_needed`, `rejected`, `publication_failed`.
- **In review:** count.
- **Armed:** `approved` items that have a `publicationDate`, with their dates.
- **Approved, no date:** count and titles — these will never publish.
- **Pipeline:** ideas by status (`idea`, `creation`, `sent`).

No drill-down, no `get_content`, no writes. If the user wants to dig in, hand off to the `calendar-audit` skill.
