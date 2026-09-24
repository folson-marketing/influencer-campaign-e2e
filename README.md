# Influencer Campaign E2E / 红人营销全流程 Skill

A Claude skill by **Folson Marketing** that runs an influencer (KOL) campaign end to end and builds a reusable creator library.

一个由 **Folson Marketing** 出品的 Claude Skill：把红人营销从头到尾跑完，并沉淀成可复用的红人资源库。

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

## 安装 / Install

1. Download this repo as a ZIP (or copy the folder containing `SKILL.md`).
2. In Claude, go to **Settings → Capabilities → Skills** and upload it.
3. Say 「开始一个红人营销 Campaign」 or "start an influencer campaign".

## Guardrails

- 不经你确认，Skill 不会发邮件、不会下单、也不会付款。Never sends, orders or pays without your confirmation.
- 只采集公开数据；邮件符合 CASL / CAN-SPAM。Public data only; CASL / CAN-SPAM friendly.
