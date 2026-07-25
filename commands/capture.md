---
description: Capture a one-line content idea straight into the Your Social Hub Ideas Hub.
argument-hint: [the idea, in a sentence]
disable-model-invocation: true
---

Capture `$ARGUMENTS` (or, if empty, what the user just described) as a single Ideas Hub entry. Fast path — no voice research, no batch.

1. Resolve the calendar: `list_hubs` → `list_content_calendars(hubId)`. Ask only if more than one is plausible; remember the choice for the rest of the session.
2. Turn the sentence into a `title` and `notes`. Add a `caption` only if the user actually wrote copy. Infer `type` from the wording (`post`, `carousel`, `story`, `thread`, `tiktok`) and default to `post`.
3. One `create_idea` call. Do not send `fileOrder`. Only set `publicationDate` if the user named a date, as full ISO-8601 UTC.
4. Report the idea id and title in one line, and note that it is planning-only until someone promotes it to content for review.

If `create_idea` is missing, the `ideas:write` scope was not granted — say so and stop.
