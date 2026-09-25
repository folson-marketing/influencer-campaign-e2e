# 初始化：数据库与红人数据源

## 数据库选项

| 选项 | 适合 | 需要什么 |
|---|---|---|
| 飞书多维表格 | 中文团队、需要看板 / 自动化 | 飞书 / Lark 相关工具，或浏览器 |
| Google Sheets | 海外团队、多人协作 | Google Drive / Sheets 相关工具 |
| 本地 Excel (.xlsx) | 个人使用、不想上云 | 能访问用户电脑上的文件；否则生成文件供下载 |

用户选完后检查对应能力。**缺什么就明确告诉用户怎么装，并让用户选：安装 / 换方案 / 用浏览器或导入文件。**

### 飞书多维表格

- 检查有没有名字含 `lark` / `feishu` / `bitable` 的工具。
- 没有：如果 Agent 能搜索连接器，就搜「feishu」「lark」「bitable」。目录里没有就告诉用户可以装飞书官方 MCP（`@larksuiteoapi/lark-mcp`）：在 open.feishu.cn 建一个企业自建应用，拿到 App ID / App Secret，开通多维表格读写权限，然后在 Agent 里作为本地 MCP 添加。
- 用户不装：用浏览器打开多维表格操作；或者先生成 xlsx，让用户在飞书里「导入 Excel 创建多维表格」，之后用浏览器更新。
- 用户需要提供（或让 Agent 新建）多维表格链接，写进 `_Config`。

### Google Sheets

- 检查有没有 Google Drive 或 Sheets 相关工具。
- 没有：如果 Agent 能搜索连接器，就搜「google sheets」「google drive」并推荐安装；否则告诉用户在 Agent 设置里添加 Google Drive 连接器。
- 用户不装：用浏览器打开 sheets.google.com 操作；或者先生成 CSV / xlsx，让用户导入 Google Sheets。
- 建好后把表格链接写进 `_Config`，并告诉用户链接。

### 本地 Excel

- **Agent 能读写用户电脑上的文件**（如 Claude Code、已连接的电脑或文件夹）：在用户文件夹里用 Python + openpyxl 直接建和改 xlsx，原地编辑，不要来回上传下载。还没连接文件夹时，先请用户授权一个文件夹。
- **Agent 访问不到用户的文件**（如云端对话）：在工作区生成文件发给用户下载；之后每次更新前让用户上传最新版，避免版本冲突。

## 红人数据源

默认用 **Apify**。
- 检查有没有 Apify 相关工具（搜索 actor、运行 actor、读取数据集等）。
- 没有：如果 Agent 能搜索连接器，就搜「apify」「scraper」「influencer」并推荐；目录里没有就告诉用户可以添加 Apify 远程 MCP（`https://mcp.apify.com`，需要 Apify 账号和 API token）。
- 用户不愿意装，或目标平台找不到合适的 actor：用浏览器直接在各平台搜索（小红书、抖音、B站、TikTok、Instagram、YouTube 的搜索页、话题页、竞品评论区），手动读取主页数据。告诉用户浏览器方式更慢，每次建议 20–30 个候选。
- 如果还有其他红人数据工具（如 Bizkol、Modash、HypeAuditor），问用户是否一并使用。

## 邮件

支持 **Gmail** 和 **Outlook / Microsoft 365**。检查对应工具，缺了就按上面的方式推荐安装；用户不装，或使用其他邮箱（企业邮箱、QQ 邮箱等），就只输出文案让用户自己发。
