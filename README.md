# Influencer Campaign E2E / 红人营销全流程 Skill

An agent skill by **Folson Marketing** that runs an influencer (KOL) campaign end to end and builds a reusable creator library. It uses the open [Agent Skills](https://agentskills.io) format (`SKILL.md`), so it works in Claude and other agents that support skills.

一个由 **Folson Marketing** 出品的 Agent Skill：把红人营销从头到尾跑完，并沉淀成可复用的红人资源库。采用开放的 Agent Skills 格式（`SKILL.md`），可在 Claude 及其他支持 Skills 的 Agent 中使用。

## 两个版本 / Two editions

| | English edition | 中文版 |
|---|---|---|
| Skill | `influencer-campaign-e2e` | `influencer-campaign-e2e-zh` |
| For | English-speaking teams | 中文团队 |
| Language | English instructions, fields and templates | 中文说明、双语字段、中文模板为主 |
| Platforms | TikTok, Instagram, YouTube first | 小红书、抖音、B站、微博、视频号 + 海外平台 |
| Database | Google Sheets / Excel first | 飞书多维表格优先 |

Both editions share the same workflow, guardrails and data-integrity rules. **Install one edition per team**; installing both means either one may answer a request.
两个版本流程、规则和数据完整性要求相同。**每个团队只安装一个版本**；两个都装的话，Agent 可能随机选用其中一个。

## 流程 / Workflow

| # | 阶段 | Stage |
|---|---|---|
| 0 | 初始化：Brief、选数据库、选数据源 | Setup |
| 1 | 找红人 | Discover |
| 2 | 评估打分（0–100，A/B/C 分级） | Evaluate & Score |
| 3 | 生成个性化外联邮件（中 / 英） | Draft Outreach |
| 4 | 发送邮件（Gmail / Outlook，默认草稿） | Send |
| 5 | 跟进 | Follow up |
| 6 | 合作管理：谈判、Brief、UTM、寄样、审稿 | Manage |
| 7 | 效果追踪：CPM / CPE / CPA / ROAS | Track Performance |
| 8 | 复盘与再次合作 | Review & Re-collab |
| 9 | 资源库维护 | Library Upkeep |

## 支持的工具 / Integrations

- **数据库 Database**：本地 Excel · Google Sheets · 飞书多维表格 (Lark Bitable)
- **红人数据源 Creator data**：Apify MCP（没装的话会提示安装，也可以改用浏览器）
- **邮件 Email**：Gmail · Outlook / Microsoft 365

缺哪个连接器，Skill 都会提示你安装；你也可以选择改用浏览器，或者先导出文件再导入。
If a connector is missing, the skill tells you how to install it, or falls back to a browser or file import.

## 安装 / Install

### Claude.ai / Claude 桌面端 Desktop

1. Download this repo as a ZIP and unzip it. 下载本仓库 ZIP 并解压。
2. Zip the edition's folder on its own: `skills/influencer-campaign-e2e/` (English) or `skills/influencer-campaign-e2e-zh/` (中文). 单独把对应版本的文件夹压缩成 ZIP：英文版 `skills/influencer-campaign-e2e/`，中文版 `skills/influencer-campaign-e2e-zh/`。
3. In Claude, go to **Settings → Capabilities → Skills** and upload that ZIP. 在 Claude 的 **设置 → 功能 → Skills** 上传该 ZIP。

### Claude Code

As a plugin (recommended, easy to update) / 作为插件安装（推荐，方便更新）:

```
/plugin marketplace add folson-marketing/influencer-campaign-e2e
/plugin install influencer-campaign-e2e@folson-marketing      # English edition
/plugin install influencer-campaign-e2e-zh@folson-marketing   # 中文版
```

Or copy the skill folder manually / 或手动复制：

```bash
git clone https://github.com/folson-marketing/influencer-campaign-e2e.git
cp -r influencer-campaign-e2e/skills/influencer-campaign-e2e ~/.claude/skills/      # English
cp -r influencer-campaign-e2e/skills/influencer-campaign-e2e-zh ~/.claude/skills/   # 中文
```

### Other agents (Codex, etc.) / 其他 Agent

Copy the edition's folder into that agent's skills directory (for Codex: `~/.codex/skills/`). Check your agent's docs for the exact path.
把对应版本的文件夹复制到该 Agent 的 skills 目录（Codex 为 `~/.codex/skills/`），具体路径以该 Agent 文档为准。

### ChatGPT custom GPTs / ChatGPT 自定义 GPT

Custom GPTs don't load `SKILL.md` folders directly. Paste the edition's `SKILL.md` into the GPT's instructions and upload the `references/` files as knowledge. Email and spreadsheet steps then need GPT Actions or fall back to copy-paste.
自定义 GPT 不能直接加载 Skill 文件夹：把对应版本的 `SKILL.md` 粘贴到 GPT 指令中，把 `references/` 里的文件作为知识库上传；邮件和表格操作需要配置 Actions，否则会改为输出文本让你手动操作。

### 开始使用 / Start

- English: say "start an influencer campaign".
- 中文：说「开始一个红人营销 Campaign」。

## 仓库结构 / Repo layout

```
.claude-plugin/marketplace.json   Claude Code marketplace: one plugin per edition
skills/influencer-campaign-e2e/     English edition
  SKILL.md
  references/                       data integrity, setup, schema, scoring, outreach
skills/influencer-campaign-e2e-zh/  中文版
  SKILL.md
  references/                       数据完整性、初始化、数据库结构、打分、外联文案
```

When you change the workflow, update both editions so they stay in step.
修改流程时请同步更新两个版本。

## Guardrails

- 不经你确认，Skill 不会发邮件、不会下单、也不会付款。Never sends, orders or pays without your confirmation.
- 只采集公开数据；邮件符合 CASL / CAN-SPAM。Public data only; CASL / CAN-SPAM friendly.
- 写入后回读校验，估算值明确标注，数据来源和搜索词全程公开。Every write is re-read and checked, estimates are labelled inline, and data sources and search terms are always stated.
