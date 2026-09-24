# Setup: database and creator data source

## Database options

| Option | Best for | Needs |
|---|---|---|
| Local Excel (`.xlsx`) | Solo use, data stays on the user's machine | Access to the user's files, or a file generated for download |
| Google Sheets | English-speaking teams, collaboration | Google Drive / Sheets tools |
| Lark (Feishu) Bitable 飞书多维表格 | Chinese-speaking teams, kanban views and automations | Lark / Feishu tools, or a browser |

After the user picks one, check that the capability exists. If it's missing, tell the user how to add it and let them choose: **install**, **use a different option**, or **use a browser / file import**.

### Local Excel

- **Agent can read and write the user's files** (e.g. Claude Code, a connected computer or folder): create and edit the `.xlsx` in place with Python + `openpyxl`. If no folder is connected yet, ask the user for access to one. Edit in place; don't bounce files back and forth.
- **Agent can't reach the user's files** (e.g. a cloud chat): generate the file in the workspace and give it to the user to download. On each later run, ask the user to upload the latest version first, to avoid version conflicts.

### Google Sheets

- Look for Google Drive or Sheets tools.
- Missing: if the agent can search for or suggest connectors, search for "google sheets" / "google drive" and recommend one. Otherwise tell the user to add a Google Drive connector in their agent's settings.
- User won't install: use a browser tool on sheets.google.com, or generate a `.csv` / `.xlsx` for the user to import into Google Sheets.
- Once created, write the sheet link into `_Config` and share it with the user.

### Lark (Feishu) Bitable

- Look for tools with `lark`, `feishu` or `bitable` in their names.
- Missing: search for "feishu" / "lark" / "bitable" connectors if the agent can. If none is listed, tell the user they can install the official Lark MCP server (`@larksuiteoapi/lark-mcp`): create a custom app at open.feishu.cn, get its App ID and App Secret, grant it Bitable read/write permissions, then add it to the agent as a local MCP server.
- User won't install: use a browser tool on the Bitable, or generate an `.xlsx` that the user imports into Feishu ("Import Excel to create Bitable"), and update it via the browser afterwards.
- The user provides a Bitable link (or asks the agent to create one). Write it into `_Config`.

## Creator data source

Default: **Apify**.

- Look for Apify tools (e.g. actor search, actor run, dataset items).
- Missing: search for "apify" / "scraper" / "influencer" connectors if the agent can. If none is listed, tell the user they can add Apify's remote MCP server at `https://mcp.apify.com` (needs an Apify account and API token).
- User won't install: use a browser tool on each platform's search pages, hashtag pages and competitor comment sections, reading profile data manually. Warn that this is slower; suggest 20–30 candidates per run.
- If other creator-data tools are available (Bizkol, Modash, HypeAuditor, …), ask the user whether to use them too.

## Email

Supports **Gmail** and **Outlook / Microsoft 365**. Look for the matching email tools. If missing, recommend a connector as above; if the user won't install one, output the email text for them to send themselves.
