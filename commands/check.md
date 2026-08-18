---
description: Check the Your Social Hub connection — which tools are available, which scopes were granted, and what that leaves you able to do.
disable-model-invocation: true
---

Diagnose the Your Social Hub connection and report, in this order:

1. Call `list_hubs`. If it fails or the tool does not exist, say so and stop here — the connector is not authorized. Point at `/your-social-hub:connect`.
2. List the hubs returned, with the user's role in each. A role below `content_creator` means hub-addressed tools and resources will come back "not found"; say that explicitly. A role below `admin` means the metrics tools will too, even when they are present — call that out separately.
3. Enumerate which of the 16 tools are present: `list_hubs`, `list_content_calendars`, `list_content`, `get_content`, `list_ideas`, `get_idea`, `list_social_accounts`, `get_hub_metrics_overview`, `list_top_performing_posts`, `create_content_calendar`, `create_idea`, `update_idea`, `create_content`, `update_content`, `promote_idea_to_content`, `restore_file`.
4. Infer the granted scopes from that and name any that are missing.
5. State which workflows are unavailable as a result — in particular, `promote_idea_to_content` requires both `content:write` and `ideas:write`, and the three metrics tools require `metrics:read` **plus** the `admin` role on the hub.

Keep it to one screen. Call nothing but `list_hubs`.
