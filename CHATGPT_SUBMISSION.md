# ChatGPT and Codex submission notes

This repository contains the portable skills and manifest needed for an OpenAI plugin submission. The MCP server remains hosted by Your Social Hub and is submitted as a remote server; do not upload or duplicate its implementation here.

## Register the development connection

1. In ChatGPT, enable **Developer mode** in **Settings → Security and login**.
2. Open **Plugins**, add a connection for `https://www.yoursocialhub.online/api/mcp/v1`, and configure its OAuth 2.1 flow.
3. Copy the resulting `plugin_asdk_app_…` identifier from the connection URL.
4. Create `.app.json` from this template, replacing the value with that identifier:

```json
{
  "apps": {
    "your-social-hub": {
      "id": "plugin_asdk_app_REPLACE_WITH_CONNECTION_ID"
    }
  }
}
```

5. Add `"apps": ["./.app.json"]` to `.codex-plugin/plugin.json` and test the plugin from the local marketplace in a new ChatGPT conversation.

Do not commit a real connection ID unless it is intended to be public and reusable by other developers.

## Public submission

Create a **With MCP** submission in the OpenAI Platform. Submit the production HTTPS MCP endpoint, add the skills in this package, scan all tools, and complete the listing, OAuth, privacy, test-case, and country-availability fields. The submission requires a verified publisher identity and reviewer-ready test credentials.
