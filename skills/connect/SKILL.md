---
name: connect
description: Connect and authorize the Your Social Hub connector, and diagnose missing tools, 401s or "not found" errors. Use right after installing this plugin, when the Your Social Hub tools are absent or failing, or when the user says "connect to Your Social Hub", "authorize YSH", "reconnect", "I don't see the hub tools".
---

# Connecting Your Social Hub

Read `hub-model` for what the tools reach and what the scopes buy.

## Before you start

The user needs a Your Social Hub account at <https://yoursocialhub.online> **and** the `content_creator` or `admin` role in at least one hub. A reviewer-only account can sign in and consent, but every tool call will come back "not found" because it holds no hub role.

State the ceiling up front so nothing surprises them: this connector can read hubs, content calendars, content and ideas, and can create or edit drafts and ideas. It cannot publish, schedule, approve, reject or delete anything, and it cannot upload media.

## Authorize

**Claude Code** — `/mcp` → `your-social-hub` → Authenticate. A browser opens: sign in to Your Social Hub, then the consent screen.

**Cowork** — Customize → Plugins (or Connectors) → Your Social Hub → Connect. Same browser flow.

The consent screen lists each scope with a checkbox, all pre-ticked. What each one buys:

| Scope                     | On the consent screen      | Unlocks                                    |
| ------------------------- | -------------------------- | ------------------------------------------ |
| `hubs:read`               | See your hubs              | `list_hubs` — required for everything else |
| `content-calendars:read`  | See your content calendars | `list_content_calendars` — also required for everything else |
| `content:read`            | Read your content          | `list_content`, `get_content`              |
| `ideas:read`              | Read your ideas            | `list_ideas`, `get_idea`                   |
| `content-calendars:write` | Create content calendars   | `create_content_calendar`                  |
| `content:write`           | Draft and edit content     | `create_content`, `update_content`         |
| `ideas:write`             | Create and edit ideas      | `create_idea`, `update_idea`               |
| both write scopes         | —                          | `promote_idea_to_content`                  |
| `metrics:read`            | Read your metrics          | `list_social_accounts`, `get_hub_metrics_overview`, `list_top_performing_posts` |

Four consequences worth saying out loud before they click:

- **`hubs:read` and `content-calendars:read` are both structural.** Content and ideas cannot be listed by hub alone, so unticking either one leaves every skill dead, no matter what else was granted.
- **`promote_idea_to_content` needs `content:write` and `ideas:write` together.** Grant only one and the plugin's central workflow silently does not exist.
- **`restore_file` appears under either write scope** but refuses per call for files on the other side, e.g. `ideas:write` alone cannot restore a content file.
- **`metrics:read` is the one scope a role can override.** Its three tools also need the `admin` role on the hub, matching the app's admin-only metrics dashboard — a `content_creator` who ticks the box still gets "not found" from all three.

## Verify

Call `list_hubs`. Then list which of the 16 tools are actually present and tell the user plainly which workflows that leaves them:

- no `hubs:read` **or** no `content-calendars:read` → nothing works, whatever else was ticked;
- all four content reads → `calendar-audit` works in full; `plan-week`, `draft-captions` and `promote-to-review` do not;
- reads plus `ideas:write` → planning and drafting work, nothing reaches the review queue;
- `ideas:read` missing → `plan-week` and `draft-captions` cannot see the backlog they date and rewrite;
- `metrics:read` granted but `yourRole` is not `admin` → `metrics-review` and `top-posts-to-ideas` are unavailable in practice; say it is the role, not the consent.

## Troubleshooting

| Symptom                                                | Cause                                                      | Fix                                                                              |
| ------------------------------------------------------ | ---------------------------------------------------------- | -------------------------------------------------------------------------------- |
| A tool is missing from the toolset                     | Its scope was not granted                                  | `/mcp` → `your-social-hub` → Clear authentication → Authenticate, tick the scope |
| `"Not found, or you do not have access to it."`        | Wrong id, another user's hub, or no `content_creator` role | Check the id and the membership in the app. Do not retry with other ids          |
| The same error from the metrics tools only             | The role is `content_creator`; metrics need `admin`        | Ask a hub admin to raise the role, or run the metrics dashboard in the app       |
| `list_hubs` works but nothing else can be reached      | `content-calendars:read` was not granted                   | Re-authorize and tick "See your content calendars"                              |
| `list_hubs` returns `[]`                               | The account belongs to no hub                              | Create or join a hub in the app                                                  |
| 401 mid-session                                        | Token expired, or the Connection was revoked               | Re-authenticate; check Profile & Billing → Connected Apps                        |
| `restore_file` errors with "content:write is required" | Only `ideas:write` was granted                             | Re-authorize with both write scopes                                              |
| Server unreachable / connection failed                 | Endpoint or network, not consent                           | Confirm <https://yoursocialhub.online> is up; retry the connect                  |

To disconnect: Profile & Billing → **Connected Apps** → revoke. That deletes the grant and its tokens.
