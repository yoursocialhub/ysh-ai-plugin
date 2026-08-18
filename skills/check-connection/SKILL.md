---
name: check-connection
description: Diagnose the Your Social Hub connection, identify available MCP tools and scopes, and explain which workflows are available. Use when the user asks whether YSH is connected, which permissions they granted, why a YSH capability is missing, or how to reconnect.
---

# Check Your Social Hub connection

Call `list_hubs`. If it is absent or fails, say the Your Social Hub MCP connection is not authorized and ask the user to reconnect it from their plugin settings.

When it succeeds:

1. List the hubs returned and the user's role in each. A role below `content_creator` can list hubs but cannot use hub-addressed tools or resources. A role below `admin` also cannot use metrics tools.
2. Enumerate which of these 16 tools are present: `list_hubs`, `list_content_calendars`, `list_content`, `get_content`, `list_ideas`, `get_idea`, `list_social_accounts`, `get_hub_metrics_overview`, `list_top_performing_posts`, `create_content_calendar`, `create_idea`, `update_idea`, `create_content`, `update_content`, `promote_idea_to_content`, `restore_file`.
3. Infer the granted scopes and name missing ones. `promote_idea_to_content` needs both `content:write` and `ideas:write`; metrics tools need `metrics:read` plus the `admin` role.
4. State the available and unavailable workflows in one concise screen.

Call nothing other than `list_hubs` during this check.
