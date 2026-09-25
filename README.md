# Influencer Campaign E2E / 红人营销全流程 Skill

An agent skill by **Folson Marketing** that runs an influencer (KOL) campaign end to end and builds a reusable creator library. It uses the open [Agent Skills](https://agentskills.io) format (`SKILL.md`), so it works in Claude and other agents that support skills.

一个由 **Folson Marketing** 出品的 Agent Skill：把红人营销从头到尾跑完，并沉淀成可复用的红人资源库。采用开放的 Agent Skills 格式（`SKILL.md`），可在 Claude 及其他支持 Skills 的 Agent 中使用。

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
2. Zip the `skills/influencer-campaign-e2e/` folder on its own. 单独把 `skills/influencer-campaign-e2e/` 文件夹压缩成 ZIP。
3. In Claude, go to **Settings → Capabilities → Skills** and upload that ZIP. 在 Claude 的 **设置 → 功能 → Skills** 上传该 ZIP。

### Claude Code

As a plugin (recommended, easy to update) / 作为插件安装（推荐，方便更新）:

```
/plugin marketplace add folson-marketing/influencer-campaign-e2e
/plugin install influencer-campaign-e2e@folson-marketing
```

Or copy the skill folder manually / 或手动复制：

```bash
git clone https://github.com/folson-marketing/influencer-campaign-e2e.git
cp -r influencer-campaign-e2e/skills/influencer-campaign-e2e ~/.claude/skills/
```

### Other agents (Codex, etc.) / 其他 Agent

Copy `skills/influencer-campaign-e2e/` into that agent's skills directory (for Codex: `~/.codex/skills/`). Check your agent's docs for the exact path.
把 `skills/influencer-campaign-e2e/` 复制到对应 Agent 的 skills 目录（Codex 为 `~/.codex/skills/`），具体路径以该 Agent 文档为准。

### ChatGPT custom GPTs / ChatGPT 自定义 GPT

Custom GPTs don't load `SKILL.md` folders directly. Paste `SKILL.md` into the GPT's instructions and upload the `references/` files as knowledge. Email and spreadsheet steps then need GPT Actions or fall back to copy-paste.
自定义 GPT 不能直接加载 Skill 文件夹：把 `SKILL.md` 粘贴到 GPT 指令中，把 `references/` 里的文件作为知识库上传；邮件和表格操作需要配置 Actions，否则会改为输出文本让你手动操作。

### 开始使用 / Start

Say "start an influencer campaign".

## 仓库结构 / Repo layout

```
.claude-plugin/            Claude Code plugin + marketplace manifests
skills/influencer-campaign-e2e/
  SKILL.md                 the skill (English edition)
  references/              data integrity, setup, schema, scoring, outreach — loaded on demand
docs/SKILL.zh-CN.md        中文原始草稿（仅供参考，Agent 不会加载）
```

`skills/influencer-campaign-e2e/` is the **English edition**: instructions, database fields, stage names and email templates are all in English. `docs/SKILL.zh-CN.md` is the original Chinese draft, kept for reference only. It predates the data-integrity rules and the English edition's field names.
`skills/influencer-campaign-e2e/` 是 **英文版**：说明、数据库字段、阶段名称和邮件模板均为英文。`docs/SKILL.zh-CN.md` 是最初的中文草稿，仅供参考，不包含数据完整性规则，字段名也与英文版不同。

## Guardrails

- 不经你确认，Skill 不会发邮件、不会下单、也不会付款。Never sends, orders or pays without your confirmation.
- 只采集公开数据；邮件符合 CASL / CAN-SPAM。Public data only; CASL / CAN-SPAM friendly.
- 写入后回读校验，估算值明确标注，数据来源和搜索词全程公开。Every write is re-read and checked, estimates are labelled inline, and data sources and search terms are always stated.
