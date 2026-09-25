# Database schema

Create these tables. In Excel use one sheet per table; in Google Sheets one tab per table; in Lark Bitable one data table per table inside a single Bitable, linked with relation fields. If a table already exists, only add missing fields; never overwrite it.

## Table 1: Creators

Long-lived across campaigns. One row per creator, deduplicated on platform + handle.

`Creator ID` · `Platform` · `Handle` · `Profile URL` · `Name` · `Location` · `Language` · `Niche` · `Followers` · `Avg Views` · `ER%` · `ER Method` (how ER was calculated) · `Posts/30d` · `Audience` (gender / age / region, only when available) · `Email` · `Other Contact` · `Rate` (per content format) · `Score` · `Score Breakdown` · `Tier (A/B/C)` · `Tags` · `# Collabs` · `Historical ROI` · `Last Contacted` · `Blacklist (Y/N + reason)` · `Source` (tool or actor) · `Search Query` · `Data Confidence` (Verified / Estimated / Proxy) · `Flags` (sanity-check warnings) · `Updated`

## Table 2: Campaigns

`Campaign ID` · `Name` · `Brand` · `Goal` · `Platforms` · `Budget` · `Dates` · `KPI` · `Brief Link` · `UTM Rule` · `Status`

## Table 3: Pipeline

Campaign × Creator. One row per collaboration.

`Campaign ID` · `Creator ID` · `Stage` (see state machine below) · `First Email` · `Follow-ups` · `Last Action` · `Next Action Date` · `Quoted` · `Agreed Fee` · `Deal Type` · `Deliverables` · `Tracking #` · `Due Date` · `UTM Link` · `Promo Code` · `Thread Link` · `Notes`

## Table 4: Content & Performance

`Campaign ID` · `Creator ID` · `Post URL` · `Posted` · `Views` · `Likes` · `Comments` · `Shares/Saves` · `Clicks` · `Conversions` · `Revenue` · `Cost` · `CPM` · `CPE` · `CPA` · `ROAS` · `Source` · `Data Confidence` · `Snapshot Date` (one snapshot each at day 3, 7 and 30 after posting)

## Table 5: _Config

Database type, links to each table, creator data source, sender mailbox, email signature, one-line brand description, and campaign defaults.

## Pipeline stage state machine

`Sourced` → `Shortlisted` → `Contacted` → `Following up` → `Replied` → `Negotiating` → `Confirmed` → `Shipped` → `In Production` → `In Review` → `Posted` → `Paid` → `Closed`

Side exits: `Declined` · `No Response` · `Rejected` (record the reason)

On every action: update Stage, Last Action and Next Action Date, and sync `Last Contacted` in the Creators table.
