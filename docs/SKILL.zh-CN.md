---
name: influencer-campaign-e2e
description: 红人营销 Campaign 全流程（E2E）：找红人、评估打分、生成并发送外联邮件、合作管理、效果追踪、跟进、复投，并建立红人资源库。Use for any end-to-end influencer/KOL campaign.
---

> 这是 Skill 的中文原版，仅供阅读参考。实际加载的 Skill 是 `skills/influencer-campaign-e2e/SKILL.md`（英文版），修改时请同步两个版本。

# 红人营销 Campaign 全流程 / Influencer Campaign E2E

把一次红人营销从头跑到尾，并把所有红人沉淀进可复用的资源库（Creator Library）。
流程共 9 个阶段，每个阶段都读写同一个数据库，所以可以随时中断、下次接着跑。

语言规则：跟随用户的语言。说明和对话用用户的语言；数据库字段名用「中文 / English」双语；外联邮件用红人所在市场的语言（北美默认英文，华语红人用中文）。

---

## 阶段 0：初始化 Setup（每个新 Campaign 必做，老 Campaign 先读配置）

### 0.1 先看有没有现成配置
如果用户给了已有的表格链接 / 文件，先读里面的 `_Config / 配置` 表，拿到数据库类型、链接、发件邮箱、品牌信息，直接跳到用户要做的阶段。没有才走下面的初始化。

### 0.2 收集 Campaign Brief（用 AskUserQuestion，一次问完）
- 品牌 / 产品、落地页 URL
- 目标：曝光 Awareness / 转化 Sales / 内容素材 UGC / 下载注册
- 目标市场与语言（如 北美英文、加拿大华人、中国大陆）
- 平台：TikTok / Instagram / YouTube / 小红书 / 其他
- 红人量级：Nano(1k–10k) / Micro(10k–100k) / Mid(100k–500k) / Macro(500k+)
- 合作方式：寄样置换 Gifting / 固定费用 Flat fee / 佣金 Affiliate / 混合
- 预算与单个红人费用上限、目标红人数量、上线时间窗口
- 内容要求（必须出现的卖点、禁用词、是否需要审稿、是否要授权二次投放）

### 0.3 选择数据库 Database（用 AskUserQuestion 让用户选）
| 选项 | 适合 | 需要什么 |
|---|---|---|
| 本地 Excel (.xlsx) | 个人使用、不想上云 | 已链接电脑的文件夹；否则在会话里生成后下载 |
| Google Sheets | 英文团队、多人协作 | Google Drive 连接器 |
| 飞书多维表格 Bitable | 中文团队、需要看板/自动化 | 飞书 / Lark MCP 或浏览器 |

用户选完后检查对应能力，**缺什么就明确告诉用户怎么装，并让用户选：安装 / 换方案 / 用浏览器**：

**本地 Excel**
- 有 `mcp__remote-devices__device_bash`：在用户文件夹里用 python + openpyxl 直接建和改 xlsx（原地编辑，不要来回上传下载）。没有连接文件夹时，调用 `device_request_folder_access` 请求一个文件夹。
- 没有链接电脑：在会话工作区用 xlsx skill 生成文件，发给用户下载；之后每次更新都让用户重新上传最新版，避免版本冲突。

**Google Sheets**
- 检查工具列表里有没有 `mcp__Google_Drive__*`（或其他 Sheets 工具）。
- 没有：用 SearchMcpRegistry 搜 ["google sheets", "google drive"]，再用 SuggestConnectors 推荐给用户安装。
- 用户不装：改用浏览器（优先内置浏览器 `Claude_Browser`，其次 Claude in Chrome）打开 sheets.google.com 操作；或者先生成 CSV/xlsx，让用户导入 Google Sheets。
- 建好后把表格链接写进 `_Config`，并告诉用户链接。

**飞书多维表格**
- 检查工具列表里有没有名字含 `lark` / `feishu` / `bitable` 的工具。
- 没有：先 SearchMcpRegistry 搜 ["feishu", "lark", "bitable"]；目录里没有就告诉用户可以装飞书官方 MCP（`@larksuiteoapi/lark-mcp`，需要在 open.feishu.cn 建一个企业自建应用，拿 App ID / App Secret，并开通多维表格读写权限），装好后在 Claude 桌面端作为本地 MCP 添加。
- 用户不装：用浏览器打开飞书多维表格操作；或者先生成 xlsx，让用户在飞书里「导入 Excel 创建多维表格」，之后再用浏览器更新。
- 用户需要提供（或让 Claude 新建）多维表格链接，写进 `_Config`。

### 0.4 选择红人数据源 Creator Data Source
默认用 **Apify**。
- 检查工具列表里有没有名字含 `Apify` 的工具（如 `search-actors`、`call-actor`、`get-dataset-items`）。
- 没有：SearchMcpRegistry 搜 ["apify", "scraper", "influencer"]，用 SuggestConnectors 推荐；目录里没有就告诉用户可以在 Claude 设置里添加 Apify 远程 MCP（https://mcp.apify.com，需要 Apify 账号和 API token）。
- 用户不愿意装：用浏览器直接在各平台搜索（TikTok / Instagram / YouTube / 小红书的搜索页、话题页、竞品评论区），手动读取主页数据。告诉用户浏览器方式更慢、每次建议 20–30 个候选。
- 如果还有其他红人工具（如 Bizkol、Modash、HypeAuditor 的 MCP），问用户是否一并使用。

### 0.5 建库 Schema
按下面结构建表（Excel 用多个 sheet；Google Sheets 用多个 tab；飞书用一个多维表格里的多张数据表，用「关联」字段连起来）。已有表就只补缺失字段，不要覆盖。

**表 1：Creators / 红人资源库**（跨 Campaign 长期积累，一个红人一行，按 平台+账号 去重）
`Creator ID` · `平台 Platform` · `账号 Handle` · `主页链接 Profile URL` · `昵称 Name` · `国家/城市 Location` · `语言 Language` · `垂类 Niche` · `粉丝数 Followers` · `平均播放 Avg Views` · `互动率 ER%` · `近30天发帖数 Posts/30d` · `受众画像 Audience`（性别/年龄/地区，能拿到才填）· `邮箱 Email` · `其他联系方式 Other Contact` · `报价 Rate`（按内容形式）· `评分 Score` · `分级 Tier (A/B/C)` · `标签 Tags` · `合作次数 # Collabs` · `历史 ROI` · `最近联系 Last Contacted` · `黑名单 Blacklist (Y/N + 原因)` · `数据来源 Source` · `更新时间 Updated`

**表 2：Campaigns / 活动**
`Campaign ID` · `名称 Name` · `品牌/产品 Brand` · `目标 Goal` · `平台 Platforms` · `预算 Budget` · `开始/结束 Dates` · `KPI` · `Brief 链接` · `UTM 规则` · `状态 Status`

**表 3：Pipeline / 合作进度**（Campaign × Creator，一次合作一行）
`Campaign ID` · `Creator ID` · `状态 Stage`（见下方状态机）· `首封邮件时间 First Email` · `跟进次数 Follow-ups` · `最近动作 Last Action` · `下一步日期 Next Action Date` · `报价 Quoted` · `成交价 Agreed Fee` · `合作方式 Deal Type` · `交付物 Deliverables` · `寄样单号 Tracking #` · `上线截止 Due Date` · `专属链接 UTM Link` · `折扣码 Promo Code` · `邮件线程 Thread Link` · `备注 Notes`

**表 4：Content & Performance / 内容与效果**
`Campaign ID` · `Creator ID` · `帖子链接 Post URL` · `发布日期 Posted` · `播放/曝光 Views` · `点赞 Likes` · `评论 Comments` · `分享/收藏 Shares/Saves` · `点击 Clicks` · `订单/转化 Conversions` · `销售额 Revenue` · `费用 Cost` · `CPM` · `CPE` · `CPA` · `ROAS` · `采集时间 Snapshot Date`（发布后第 3 / 7 / 30 天各记一次）

**表 5：_Config / 配置**：数据库类型、各表链接、数据源、发件邮箱、签名、品牌一句话介绍、Campaign 默认参数。

### Pipeline 状态机 Stage
`待评估 Sourced` → `已入选 Shortlisted` → `已联系 Contacted` → `跟进中 Following up` → `已回复 Replied` → `谈判中 Negotiating` → `已确认 Confirmed` → `已寄样 Shipped` → `创作中 In Production` → `待审稿 In Review` → `已发布 Posted` → `已结算 Paid` → `已复盘 Closed`
旁支：`已拒绝 Declined` · `无回复 No Response` · `淘汰 Rejected`（写原因）

每次动作都要：更新 Stage、Last Action、Next Action Date，并同步 Creators 表的 Last Contacted。

---

## 阶段 1：找红人 Discover
1. **先查资源库**：在 Creators 表里筛选垂类、平台、Tier A/B、非黑名单、近 60 天没被联系过的人，优先复用。
2. **再补新人（Apify）**：
   - 用 `search-actors` 按平台搜索当下评分高、维护中的 actor（如 TikTok / Instagram / YouTube 的 profile、hashtag、search scraper），用 `fetch-actor-details` 看输入参数，不要硬编码 actor 名。
   - 搜索策略：品类关键词 + 话题标签 + 竞品品牌名（找给竞品做过内容的红人）+ 目标地区。
   - 先跑小批量（如每个关键词 20–50 条）确认数据质量和成本，再告诉用户预估消耗后扩量。
   - 用 `get-dataset-items` 取结果，提取：账号、粉丝、近 10–20 条内容的播放/互动、简介里的邮箱和外链（Linktree 等）。
3. **浏览器方式**：在平台搜索页 / 话题页逐个打开主页，读取同样字段。
4. 写入 Creators（去重合并，已存在则更新数据）和 Pipeline（Stage = 待评估）。

## 阶段 2：评估打分 Evaluate & Score
对每个候选打 0–100 分，写入 `Score` 和打分理由：
| 维度 | 权重 | 看什么 |
|---|---|---|
| 受众匹配 Audience fit | 25 | 地区、语言、年龄/性别与目标一致 |
| 内容匹配 Content fit | 20 | 垂类、调性、是否适合产品出镜 |
| 互动质量 Engagement | 20 | ER 与同量级基准对比；评论是否真实 |
| 播放稳定性 Consistency | 15 | 近期播放中位数 / 粉丝数，是否忽高忽低 |
| 活跃度 Activity | 10 | 近 30 天发帖频率 |
| 商业化 Brand safety & commercial | 10 | 广告频率不过高、无争议内容、合作过同类品牌 |

参考 ER 基准（按平台和量级调整，数据是经验值不是硬标准）：Nano > 4%、Micro 2–4%、Mid 1.5–3%、Macro 1–2%。

**假粉 / 风险信号**（出现就扣分并在备注说明）：粉丝多但播放极低；评论大量是表情或通用夸赞；粉丝曲线突增；受众地区与内容语言不符；近期内容有争议。

分级：≥75 → A，60–74 → B，<60 → C（C 默认不联系）。把 Top 名单按分数排序给用户确认，用户确认后 Stage → 已入选。

## 阶段 3：生成邮件 Draft Outreach
- 每封都要个性化：提到对方一条具体内容 + 为什么适合这个产品 + 清晰的合作方式 + 一个简单的下一步（回复 / 填表 / 约通话）。
- 短：首封 80–150 词；标题 < 50 字符，不要全大写和夸张符号。
- 不编造数据、不承诺做不到的费用或权益。
- 结尾带退订说明（如「If this isn't a fit, just let me know and I won't follow up.」），符合 CASL / CAN-SPAM。

**首封模板骨架（英文）**
```
Subject: {Brand} x {Creator first name} — {one-line hook}

Hi {Name},

I loved your {specific video/post} about {topic} — {one genuine detail}.

I'm {Sender} from {Brand}. We make {one-line product}. Your audience {why fit}.

We'd love to {deal: send you {product} / a paid {deliverable} for {fee range or "your rate"}}.

Would you be open to it? Happy to share details.

{Signature}
P.S. If it's not a fit, no worries — just let me know.
```

**首封模板骨架（中文）**
```
标题：{品牌} × {红人昵称}｜{一句话亮点}

{昵称}你好，

看了你那条关于{主题}的{视频/笔记}，{一个真实细节}，很有共鸣。

我是{品牌}的{发件人}，我们做{一句话产品}。觉得你的粉丝{匹配原因}。

想邀请你{合作方式：体验寄样 / 付费合作{交付物}}。

方便的话回复一下，我把详细方案发你～

{签名}
（如果暂时不合适也没关系，回复告诉我就不再打扰）
```

把草稿以列表形式给用户过目（红人、分数、标题、正文），用户可逐封修改。

## 阶段 4：发送邮件 Send
支持 **Gmail** 和 **Outlook / Microsoft 365**。先问用户用哪个邮箱（记进 `_Config`）。
- 检查对应工具：`mcp__Gmail__*` 或 `mcp__Microsoft_365__*`。缺了就用 SearchMcpRegistry + SuggestConnectors 推荐安装；用户不装就只输出文案让用户自己发。
- **默认先建草稿**（Gmail `create_draft` / Outlook `outlook_create_draft`），告诉用户去邮箱检查。只有用户明确说「发送」才调用发送工具，并且在发送前列出收件人清单再确认一次。
- 分批发：每批 ≤ 20 封，避免被判垃圾邮件。
- 没有邮箱的红人：给出 DM 版本短文案（≤ 300 字符），让用户在平台私信手动发。
- 发送后：Stage → 已联系，记录 First Email、Thread Link，Next Action Date = +4 天。

## 阶段 5：跟进 Follow up
每次运行时先扫描 Pipeline 里 `Next Action Date ≤ 今天` 的行，并用邮件搜索（Gmail `search_threads` / Outlook `outlook_email_search`）查有没有新回复。
- 节奏：首封后 +4 天第 1 次跟进，再 +5 天第 2 次（最后一次，语气轻松、给出「不合适也没关系」）。2 次后仍无回复 → Stage = 无回复，90 天内不再联系。
- 跟进要在原线程回复（Gmail `reply` / Outlook `outlook_create_reply_draft`），同样默认草稿。
- 有回复时：总结对方意图（接受 / 报价 / 问问题 / 拒绝），给出建议回复草稿，更新 Stage。

## 阶段 6：合作管理 Manage
- **谈判**：把对方报价与预算、同量级市场价、红人分数对比，给用户谈判建议（如打包多条内容、加佣金降固定费）。记录 Agreed Fee、Deal Type。
- **确认后发 Brief**：一页纸，包含产品卖点、必须包含 / 禁止内容、话术示例、CTA、专属链接和折扣码、交付物与截止日期、审稿流程、授权与披露要求（#ad / 合作标注，按平台和当地法规）。
- **专属追踪**：为每个红人生成 UTM 链接（`utm_source={platform}&utm_medium=influencer&utm_campaign={campaign_id}&utm_content={handle}`）；如果用户用 Shopify 且已连接，可以为每个红人建专属折扣码。
- **寄样**：收集地址（只存在合作表里，不写入资源库公开字段），记录单号，Stage → 已寄样。
- **提醒**：截止日前 3 天自动生成友好提醒草稿。
- **审稿**：对照 Brief 检查初稿，列出需要修改的点。
- **付款**：发布并核对后 Stage → 已结算。

## 阶段 7：效果追踪 Track Performance
- 在发布后第 3 / 7 / 30 天采集数据：用 Apify 抓帖子链接的播放、点赞、评论、分享；或浏览器读取；或让用户上传红人给的后台截图。
- 转化数据：UTM 点击和转化从 GA4 / 用户的分析工具取；折扣码订单从 Shopify 取（若已连接）。
- 计算：CPM = 费用 / 播放 × 1000；CPE = 费用 / 互动；CPA = 费用 / 转化；ROAS = 销售额 / 费用（寄样把产品成本 + 运费算进费用）。
- 输出 Campaign 汇总：总花费、总曝光、总互动、转化、ROAS、Top 5 / Bottom 5 红人、最好的内容角度和钩子。用户要可视化时先加载 dataviz skill。

## 阶段 8：复盘与再次合作 Review & Re-collab
- 按效果更新 Creators 表：历史 ROI、合作次数、Tier（效果好升 A，差降 C），写合作体验备注（响应速度、配合度、是否准时）。违约或内容违规 → 黑名单并写原因。
- A 级红人：生成复投邀请草稿（提到上次的好数据、提出长期合作 / 年框 / 佣金升级）。
- 效果好的内容：建议用户申请授权做付费投放（Spark Ads / Partnership Ads）。
- Campaign Stage → 已复盘，所有 Pipeline 行关闭。

## 阶段 9：资源库维护 Library Upkeep（每次运行顺手做）
- 去重合并同一红人。
- 数据超过 90 天的，下次用前先刷新粉丝和播放。
- 给用户一句话报告：库里总人数、各 Tier 人数、本次新增人数。

---

## 每次运行的默认动作
用户只说「跑一下红人 Campaign」或类似时：
1. 读 `_Config` 和 Pipeline。
2. 列出今天要做的事：待跟进、有新回复、快到截止、待采集数据。
3. 问用户要推进哪些（AskUserQuestion，多选），然后逐个执行。

## 规则 Guardrails
- **不经用户确认绝不发送邮件、不下单、不付款。**默认只建草稿。
- 只采集公开数据；遵守平台条款；不抓私密账号；个人住址和电话只存在合作表，不外发。
- Apify 每次扩量前说明预计结果数和大致成本。
- 所有邮件包含退订 / 不再打扰说明，尊重「不需要」的回复并立即标记。
- 不编造红人数据、效果数据或报价；拿不到的字段留空并注明。
- 写数据库前先读最新版本，只追加或更新对应行，不整表覆盖。
- 每个阶段结束用 1–2 句话告诉用户结果和下一步，并附数据库链接。
