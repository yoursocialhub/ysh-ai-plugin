---
description: One-screen performance snapshot for a Your Social Hub hub — followers, posts, engagement and views over the window, per account, with the top posts behind them.
argument-hint: [hub name] [7d|30d|90d]
disable-model-invocation: true
---

Produce a fixed-shape performance snapshot for the hub named in `$ARGUMENTS`, over the range named there (`7d`, `30d` or `90d`; default `30d`).

1. `list_hubs` to resolve the name to an id. Ask which one if it is empty or ambiguous. If `yourRole` is not `admin`, stop here and say the metrics tools need the admin role on that hub.
2. `get_hub_metrics_overview(hubId, range)`.
3. `list_top_performing_posts(socialAccountId, metric: 'views', range, perPage: 5)` once per account that reported numbers.

Then report exactly this, and nothing more:

- **Hub totals** over the window: followers, posts, engagement, views. Note next to them if any account contributed nothing.
- **Per account:** name, platform, followers, posts in range, engagement in range.
- **Accounts with no data:** name, platform and `metricsStatus` — still collecting, collection failed, or the platform reports no insights.
- **Top 3 posts per reporting account:** views, type, publish date, permalink, first line of the caption.

No drill-down, no `get_content`, no second range, no writes. Report `null` as "not collected", never as `0`. If the user wants to dig in, hand off to the `metrics-review` skill; to turn the winners into ideas, `top-posts-to-ideas`.
