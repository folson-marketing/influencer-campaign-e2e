# 数据库结构

按下面结构建表：Excel 用多个 sheet；Google Sheets 用多个 tab；飞书用一个多维表格里的多张数据表，用「关联」字段连起来。已有表就只补缺失字段，不要覆盖。

字段名采用「中文 / English」双语，方便中英文团队共用一个数据库。

## 表 1：红人资源库 Creators

跨 Campaign 长期积累，一个红人一行，按平台 + 账号去重。

`Creator ID` · `平台 Platform` · `账号 Handle` · `主页链接 Profile URL` · `昵称 Name` · `国家/城市 Location` · `语言 Language` · `垂类 Niche` · `粉丝数 Followers` · `平均播放 Avg Views` · `互动率 ER%` · `互动率算法 ER Method` · `近30天发帖数 Posts/30d` · `受众画像 Audience`（性别 / 年龄 / 地区，能拿到才填）· `邮箱 Email` · `微信/其他联系方式 Other Contact` · `MCN/经纪 Agency` · `报价 Rate`（按内容形式）· `评分 Score` · `各维度得分 Score Breakdown` · `分级 Tier (A/B/C)` · `标签 Tags` · `合作次数 # Collabs` · `历史 ROI` · `最近联系 Last Contacted` · `黑名单 Blacklist (Y/N + 原因)` · `数据来源 Source` · `搜索词 Search Query` · `数据可信度 Data Confidence`（已核实 / 估算 / 替代指标）· `风险提示 Flags` · `更新时间 Updated`

## 表 2：活动 Campaigns

`Campaign ID` · `名称 Name` · `品牌/产品 Brand` · `目标 Goal` · `平台 Platforms` · `预算 Budget` · `开始/结束 Dates` · `KPI` · `Brief 链接 Brief Link` · `UTM 规则 UTM Rule` · `状态 Status`

## 表 3：合作进度 Pipeline

Campaign × 红人，一次合作一行。

`Campaign ID` · `Creator ID` · `阶段 Stage`（见下方状态机）· `首封邮件时间 First Email` · `跟进次数 Follow-ups` · `最近动作 Last Action` · `下一步日期 Next Action Date` · `报价 Quoted` · `成交价 Agreed Fee` · `合作方式 Deal Type` · `交付物 Deliverables` · `寄样单号 Tracking #` · `上线截止 Due Date` · `专属链接 UTM Link` · `折扣码 Promo Code` · `邮件线程 Thread Link` · `备注 Notes`

## 表 4：内容与效果 Content & Performance

`Campaign ID` · `Creator ID` · `帖子链接 Post URL` · `发布日期 Posted` · `播放/曝光 Views` · `点赞 Likes` · `评论 Comments` · `分享/收藏 Shares/Saves` · `点击 Clicks` · `订单/转化 Conversions` · `销售额 Revenue` · `费用 Cost` · `CPM` · `CPE` · `CPA` · `ROAS` · `数据来源 Source` · `数据可信度 Data Confidence` · `采集时间 Snapshot Date`（发布后第 3 / 7 / 30 天各记一次）

## 表 5：配置 _Config

数据库类型、各表链接、红人数据源、发件邮箱、签名、品牌一句话介绍、Campaign 默认参数。

## 合作阶段状态机

`待评估 Sourced` → `已入选 Shortlisted` → `已联系 Contacted` → `跟进中 Following up` → `已回复 Replied` → `谈判中 Negotiating` → `已确认 Confirmed` → `已寄样 Shipped` → `创作中 In Production` → `待审稿 In Review` → `已发布 Posted` → `已结算 Paid` → `已复盘 Closed`

旁支：`已拒绝 Declined` · `无回复 No Response` · `淘汰 Rejected`（写原因）

每次动作都要：更新阶段、最近动作、下一步日期，并同步红人资源库的最近联系时间。
