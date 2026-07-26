# Your Social Hub plugin

Connects Claude to [Your Social Hub](https://www.yoursocialhub.online) and teaches it how to work there. Works in Claude Cowork and Claude Code.

Reads your hubs, content calendars, content and Ideas Hub entries. Writes ideas and content **drafts**, which always land `in_review`. It cannot publish, schedule, approve, reject or delete anything, and it cannot upload media — a person approving in the Review Portal remains the only path to a live post.

Full capability reference: [info.md](info.md).

## Install

**Claude Cowork** — [step-by-step guide with screenshots](install-guide/cowork.md), five minutes, no terminal.

**Claude Code** — run these three commands in Claude Code, in order:

1. Add the marketplace:

```
/plugin marketplace add yoursocialhub/ysh-ai-plugin
```

2. Install the plugin:

```
/plugin install your-social-hub@ysh-plugins
```

3. Connect the connector — pick `your-social-hub`, then Authenticate:

```
/mcp
```

Installing and connecting are separate steps, and both are required. Connecting opens a browser: sign in to Your Social Hub, then tick the scopes on the consent screen. You need the `content_creator` or `admin` role in at least one hub — a reviewer-only account can consent but reaches no data.

## Contents

| Component                       | What it is                                                                   |
| ------------------------------- | ---------------------------------------------------------------------------- |
| MCP connector `your-social-hub` | `https://www.yoursocialhub.online/api/mcp/v1`, OAuth 2.1 with PKCE, 13 tools |
| Skill `hub-model`                | Domain model, tool map and the traps — background for every other skill      |
| Skill `connect`                 | Authorization walkthrough and troubleshooting                                |
| Skill `plan-week`               | Date the Ideas Hub backlog into a week or month                              |
| Skill `promote-to-review`       | Move finished ideas into the review queue as drafts                          |
| Skill `calendar-audit`          | Read-only health report on a content calendar                                |
| Skill `draft-captions`          | Draft or rewrite copy into the Ideas Hub, grounded in existing voice         |
| `/your-social-hub:check`        | Which tools and scopes you actually have                                     |
| `/your-social-hub:brief`        | Fixed one-screen calendar status                                             |
| `/your-social-hub:capture`      | One sentence → one idea                                                      |

## Scopes

The connector pins the scopes it requests:

```
hubs:read content-calendars:read content:read ideas:read
content-calendars:write content:write ideas:write offline_access
```

The consent screen lets you grant a narrower set. An ungranted scope means its tools are absent from the toolset rather than failing — `/your-social-hub:check` reports exactly what that leaves. `promote_idea_to_content` needs `content:write` **and** `ideas:write`. Widening the pinned set requires a plugin version bump.

Revoke any time in Your Social Hub: Profile & Billing → Connected Apps.

## Development

The plugin is developed here and installed from here; the MCP server it talks to lives in the Your Social Hub platform repository.

```bash
claude plugin validate . --strict     # manifest, skill and command frontmatter
/plugin marketplace add ./            # install this working copy
/reload-plugins                       # after editing anything but a SKILL.md
```

`version` in `.claude-plugin/plugin.json` gates updates: bump it on every release, or installed users keep the cached copy.
