# What this plugin can do

A capability reference for the Your Social Hub plugin: every tool it exposes, every scope it asks for, and the hard limits it works within. If you are deciding whether to install it, or trying to work out why something is missing, this is the page.

## In one paragraph

The plugin connects Claude to your Your Social Hub account over OAuth 2.1. Claude can then read your hubs, content calendars, content, Ideas Hub entries and the performance metrics of your connected social accounts, and write **drafts** — ideas, content awaiting review, new calendars. It cannot publish, schedule, approve, reject or delete anything, and it cannot upload media. Every draft it creates enters the same review queue a person would use.

## The connector

| | |
| --- | --- |
| Endpoint | `https://www.yoursocialhub.online/api/mcp/v1` |
| Transport | Streamable HTTP, stateless |
| Auth | OAuth 2.1, PKCE, dynamic client registration |
| Token | JWT, verified on every call; nothing is cached server-side |
| Revoke | Profile & Billing → Connected Apps |

Sign-in happens in your browser, on the Your Social Hub domain. The plugin never sees your password.

## Tools

### Reading

| Tool | Takes | Gives back |
| --- | --- | --- |
| `list_hubs` | — | Every hub you belong to and your role in it |
| `list_content_calendars` | `hubId`, optional `search`, `page`, `perPage` | Calendars in that hub, paginated |
| `list_content` | `contentCalendarId`, optional `status`, `type`, `dateFrom`, `dateTo`, `page`, `perPage` | Content in that calendar, paginated |
| `get_content` | `id` | One content item in full, plus files deleted in the last 24h |
| `list_ideas` | `contentCalendarId`, optional `status`, `tab`, `search`, `startDate`, `endDate` | Ideas in that calendar (not paginated) |
| `get_idea` | `id` | One idea in full, plus files deleted in the last 24h |

### Reading metrics

Requires the `admin` role on the hub — the same gate as the metrics dashboard in the app. `range` is `7d`, `30d` or `90d`, defaulting to `30d`; there is no custom window and nothing older than 90 days.

| Tool | Takes | Gives back |
| --- | --- | --- |
| `list_social_accounts` | `hubId`, optional `range` | Every social account connected to the hub, each with followers, posts and engagement over the window |
| `get_hub_metrics_overview` | `hubId`, optional `range` | Hub-wide totals — followers, posts, engagement, views — plus the same per-account breakdown |
| `list_top_performing_posts` | `socialAccountId`, optional `metric`, `range`, `page`, `perPage` | That account's posts in the window, ranked by the chosen metric, with reach, views, reactions, comments, shares, saves and the rest |

A metric a platform does not report comes back `null` and sorts last — `null` means *not collected*, never zero. Live collection currently covers Facebook and Instagram; other connected platforms can return an account row with every number empty until collection reaches them.

### Writing

| Tool | Takes | Effect |
| --- | --- | --- |
| `create_content_calendar` | `hubId`, `name`, optional `description`, `dueDate` | New calendar, subject to your plan's limit |
| `create_idea` | `contentCalendarId`, `type`, `title`, optional `notes`, `caption`, `hashtags`, `cta`, `refLink`, `publicationDate` | New Ideas Hub entry at status `idea` |
| `update_idea` | `id` + any editable field, optional `fileOrder` | Edits an idea; blocked once it has been promoted |
| `create_content` | `contentCalendarId`, `type`, optional `name`, `caption` | New content draft at status `in_review` |
| `update_content` | `id` + any editable field, optional `fileOrder` | Edits a draft, only while `in_review` or `revisions_needed` |
| `promote_idea_to_content` | `ideaId` | Copies the idea's text, date and files into a new draft for review; marks the idea `sent` |
| `restore_file` | `fileId` | Restores a file removed within the last 24h |

Data never leaves the field-by-field allowlist above — social-account credentials, tokens and storage keys are stripped before anything reaches the model.

## Scopes

You tick these individually on the consent screen. An ungranted scope does not make its tools fail; it makes them **absent**, so Claude cannot call them at all.

| Scope | Grants |
| --- | --- |
| `hubs:read` | `list_hubs` — required for everything else |
| `content-calendars:read` | `list_content_calendars` |
| `content:read` | `list_content`, `get_content` |
| `ideas:read` | `list_ideas`, `get_idea` |
| `metrics:read` | `list_social_accounts`, `get_hub_metrics_overview`, `list_top_performing_posts` — hub role `admin` on top of the scope |
| `content-calendars:write` | `create_content_calendar` |
| `content:write` | `create_content`, `update_content` |
| `ideas:write` | `create_idea`, `update_idea` |
| `content:write` + `ideas:write` | `promote_idea_to_content` |
| either write scope | `restore_file`, for files on the matching side |

Beyond scopes, every call is checked against your hub membership: you need the `content_creator` or `admin` role in the hub that owns the data, and `admin` specifically for the metrics tools. A reviewer-only account can connect but will reach nothing.

## What it cannot do, by design

- **Publish.** There is no publish, schedule or approve tool. Content created here is always `in_review`, and a person approving in the Review Portal is the only path to a live post.
- **Delete.** No tool deletes content, ideas or calendars. Removing a file from an idea or draft moves it to a trash that stays restorable for 24 hours.
- **Upload media.** Binaries cannot travel over this connector. Ask Claude to write the shot list into an idea's notes, and attach files in the app.
- **Touch social accounts.** It reads derived performance figures and nothing else — connecting, disconnecting or re-authorizing an account is out of reach, and access tokens, refresh tokens and every other credential field never enter a tool result. There is no way to edit, boost or repost a live post either.
- **Cross into someone else's hub.** The grant is yours; it reaches exactly the hubs you are a member of.

## What it is good at

- "What is stuck in the August calendar?" — a status sweep that separates *waiting on a reviewer* from *waiting on you*, and flags approved items with no date, which will never publish.
- "Plan next week from the Ideas Hub." — proposes dates for undated ideas, checks the slots against content already scheduled, then writes the dates back.
- "Draft five carousel ideas for the launch, in our usual voice." — reads recent entries for tone and conventions first, shows the batch for approval, then saves them.
- "These three ideas are ready — send them for review." — promotes each into a draft, and tells you what a person still has to do in the app.
- "How did the accounts do this month?" — hub totals and a per-account breakdown over 7, 30 or 90 days, with the accounts that reported no data named rather than counted as zero.
- "What worked best, and give me more of it." — ranks posts by reach, saves, shares or clicks, works out what the winners have in common, and writes the follow-ups into the Ideas Hub.

## Included skills and commands

| Component | Purpose |
| --- | --- |
| Skill `hub-model` | The domain model, the tool map and the traps. Loaded before other work |
| Skill `connect` | Authorization walkthrough and troubleshooting |
| Skill `plan-week` | Date the Ideas Hub backlog |
| Skill `promote-to-review` | Move finished ideas into the review queue |
| Skill `calendar-audit` | Read-only health report on a calendar |
| Skill `draft-captions` | Draft or rewrite copy into the Ideas Hub |
| Skill `metrics-review` | Read-only performance report across a hub's connected accounts |
| Skill `top-posts-to-ideas` | Mine the best performing posts into new Ideas Hub entries |
| `/your-social-hub:check` | Which tools and scopes you actually have |
| `/your-social-hub:brief` | Fixed one-screen calendar status |
| `/your-social-hub:metrics` | Fixed one-screen performance snapshot |
| `/your-social-hub:capture` | One sentence → one idea |

## A note on trust

Captions, titles and notes in your hubs are written by people, and tool results carry that text verbatim. The server labels it as data, and the skills instruct Claude to report such text rather than act on it — but treat a connected assistant the way you would treat a teammate with your permissions, and keep confirmations on writes.
