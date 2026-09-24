# Database schema

Create these tables. In Excel use one sheet per table; in Google Sheets one tab per table; in Lark Bitable one data table per table inside a single Bitable, linked with relation fields. If a table already exists, only add missing fields; never overwrite it.

Field names are bilingual (`中文 / English`) so mixed-language teams can share one database.

## Table 1: Creators / 红人资源库

Long-lived across campaigns. One row per creator, deduplicated on platform + handle.

`Creator ID` · `平台 Platform` · `账号 Handle` · `主页链接 Profile URL` · `昵称 Name` · `国家/城市 Location` · `语言 Language` · `垂类 Niche` · `粉丝数 Followers` · `平均播放 Avg Views` · `互动率 ER%` · `近30天发帖数 Posts/30d` · `受众画像 Audience` (gender / age / region, only when available) · `邮箱 Email` · `其他联系方式 Other Contact` · `报价 Rate` (per content format) · `评分 Score` · `分级 Tier (A/B/C)` · `标签 Tags` · `合作次数 # Collabs` · `历史 ROI` · `最近联系 Last Contacted` · `黑名单 Blacklist (Y/N + reason)` · `数据来源 Source` · `更新时间 Updated`

## Table 2: Campaigns / 活动

`Campaign ID` · `名称 Name` · `品牌/产品 Brand` · `目标 Goal` · `平台 Platforms` · `预算 Budget` · `开始/结束 Dates` · `KPI` · `Brief 链接 Brief Link` · `UTM 规则 UTM Rule` · `状态 Status`

## Table 3: Pipeline / 合作进度

Campaign × Creator. One row per collaboration.

`Campaign ID` · `Creator ID` · `状态 Stage` (see state machine below) · `首封邮件时间 First Email` · `跟进次数 Follow-ups` · `最近动作 Last Action` · `下一步日期 Next Action Date` · `报价 Quoted` · `成交价 Agreed Fee` · `合作方式 Deal Type` · `交付物 Deliverables` · `寄样单号 Tracking #` · `上线截止 Due Date` · `专属链接 UTM Link` · `折扣码 Promo Code` · `邮件线程 Thread Link` · `备注 Notes`

## Table 4: Content & Performance / 内容与效果

`Campaign ID` · `Creator ID` · `帖子链接 Post URL` · `发布日期 Posted` · `播放/曝光 Views` · `点赞 Likes` · `评论 Comments` · `分享/收藏 Shares/Saves` · `点击 Clicks` · `订单/转化 Conversions` · `销售额 Revenue` · `费用 Cost` · `CPM` · `CPE` · `CPA` · `ROAS` · `采集时间 Snapshot Date` (one snapshot each at day 3, 7 and 30 after posting)

## Table 5: _Config / 配置

Database type, links to each table, creator data source, sender mailbox, email signature, one-line brand description, and campaign defaults.

## Pipeline stage state machine

`待评估 Sourced` → `已入选 Shortlisted` → `已联系 Contacted` → `跟进中 Following up` → `已回复 Replied` → `谈判中 Negotiating` → `已确认 Confirmed` → `已寄样 Shipped` → `创作中 In Production` → `待审稿 In Review` → `已发布 Posted` → `已结算 Paid` → `已复盘 Closed`

Side exits: `已拒绝 Declined` · `无回复 No Response` · `淘汰 Rejected` (record the reason)

On every action: update Stage, Last Action and Next Action Date, and sync `Last Contacted` in the Creators table.
