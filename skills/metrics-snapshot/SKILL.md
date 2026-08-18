---
name: metrics-snapshot
description: Produce a fixed, one-screen Your Social Hub performance snapshot across a hub's connected social accounts. Use when the user asks for followers, posts, engagement, views, or top posts over 7, 30, or 90 days.
---

# Metrics snapshot

Produce a fixed performance snapshot for the hub and range the user names. Accept `7d`, `30d`, or `90d`; default to `30d`.

1. Resolve the hub with `list_hubs`. If the user's role is not `admin`, stop and explain that metrics tools require the admin role.
2. Call `get_hub_metrics_overview` for the requested range.
3. For each account reporting numbers, call `list_top_performing_posts` with `metric: "views"` and `perPage: 5`.

Report exactly:

- **Hub totals:** followers, posts, engagement, and views; note if any account did not contribute.
- **Per account:** name, platform, followers, posts in range, and engagement in range.
- **Accounts with no data:** name, platform, and `metricsStatus`.
- **Top 3 posts per reporting account:** views, type, publication date, permalink, and first caption line.

Do not call `get_content`, request another range, or write data. Report `null` as “not collected”, never as zero. Hand off to `metrics-review` for deeper analysis or `top-posts-to-ideas` to turn winners into ideas.
