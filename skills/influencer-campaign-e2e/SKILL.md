---
name: influencer-campaign-e2e
description: Run an influencer / KOL marketing campaign end to end — discover creators, score them, draft and send personalized outreach, follow up, manage collaborations (negotiation, briefs, UTM links, product seeding, content review), track performance (CPM / CPE / CPA / ROAS), re-engage top performers, and maintain a reusable creator library in Excel, Google Sheets or Lark Bitable. Use for any influencer, creator or KOL campaign, outreach, or creator-database task. 红人营销 / 达人合作 / KOL 投放全流程。
---

# Influencer Campaign E2E

Run an influencer campaign from brief to wrap-up, and keep every creator in a reusable **Creator Library**. The workflow has 9 stages. Every stage reads and writes the same database, so a campaign can be paused at any point and resumed in a later session.

## Language

- Talk to the user in the language they use.
- Name database fields bilingually, `中文 / English` (e.g. `平台 Platform`), so Chinese- and English-speaking teams can share one database.
- Write outreach in the creator's market language: English by default for North America, Chinese for Chinese-speaking creators.

## Tools: use capabilities, not fixed tool names

This skill runs in different agents (Claude.ai, Claude Code, Codex and others), and each exposes different tools. Instructions below describe a **capability**. Use whichever tool provides it; if none does, use the fallback.

| Capability | Use | Fallback when missing |
|---|---|---|
| Ask the user | A structured multiple-choice question tool | Ask in chat with a numbered list of options |
| Spreadsheet / database | Excel (local files), Google Sheets / Drive, or Lark (Feishu) Bitable tools | Generate an `.xlsx` / `.csv` file for the user to import |
| Creator data | Apify, or another creator-data tool (Modash, HypeAuditor, …) | Browser tool on the platform's search pages; else ask the user to paste profile data |
| Email | Gmail or Outlook / Microsoft 365 tools | Output the email text for the user to send |
| Web browsing | Any browser-automation or page-fetch tool | Ask the user to open the page and paste what's needed |
| Store / promo codes | Shopify tools | Ask the user to create codes and paste them back |

When a capability is missing:
1. If the agent can search for or suggest connectors / MCP servers, use that to recommend one.
2. Otherwise tell the user exactly what to install (see `references/setup.md`).
3. Let the user choose: **install it**, **use the fallback**, or **pick a different option**. Never stall silently.

## Stage 0 — Setup

Do this for every new campaign. For an existing campaign, read the config first.

1. **Existing config?** If the user gives a database link or file, read its `_Config / 配置` table (database type, links, sender mailbox, brand info) and jump to the stage they want.
2. **Collect the brief** in one round of questions: brand / product and landing page URL; goal (awareness, sales, UGC, sign-ups); target market and language; platforms (TikTok, Instagram, YouTube, Xiaohongshu, other); creator tier (Nano 1k–10k, Micro 10k–100k, Mid 100k–500k, Macro 500k+); deal type (gifting, flat fee, affiliate, hybrid); total budget, per-creator cap, number of creators, go-live window; content requirements (must-say points, banned words, review needed, paid-usage rights).
3. **Choose the database**: local Excel, Google Sheets or Lark Bitable. Check the matching capability and handle gaps as above. Details: `references/setup.md`.
4. **Choose the creator data source**: Apify by default. Details: `references/setup.md`.
5. **Create the schema** from `references/schema.md`. If tables already exist, only add missing fields; never overwrite.

## Stage 1 — Discover

1. **Library first.** Filter Creators by niche, platform, Tier A/B, not blacklisted, and not contacted in the last 60 days. Reuse these before sourcing new people.
2. **Source new creators with Apify** (or another creator-data tool):
   - Search for well-rated, maintained actors per platform (profile, hashtag and search scrapers) and read each actor's input schema before running it. Don't hard-code actor names.
   - Search with category keywords + hashtags + competitor brand names (creators who've posted for competitors) + target region.
   - Run a small batch first (20–50 results per keyword) to check data quality and cost, then tell the user the estimated cost before scaling up.
   - Extract: handle, followers, views and engagement on the last 10–20 posts, and email / link-in-bio from the profile.
3. **Browser fallback:** open profiles from platform search and hashtag pages and read the same fields. Tell the user this is slower; aim for 20–30 candidates per run.
4. Write to Creators (dedupe on platform + handle; update existing rows) and Pipeline (Stage = `待评估 Sourced`).

## Stage 2 — Evaluate & score

Score each candidate 0–100 with the rubric in `references/scoring.md`, and write the score and the reasoning to the database. Tiers: **A** ≥ 75, **B** 60–74, **C** < 60 (C is not contacted by default). Show the user the ranked shortlist; after they confirm, set Stage = `已入选 Shortlisted`.

## Stage 3 — Draft outreach

Write a personalized first email for every shortlisted creator, following the rules and EN / ZH templates in `references/outreach.md`. Show the drafts as a list (creator, score, subject, body) and let the user edit each one.

## Stage 4 — Send

1. Ask which mailbox to use (Gmail or Outlook / Microsoft 365) and save it in `_Config`.
2. **Create drafts by default** and tell the user to review them in their mailbox. Send only when the user explicitly says to, and list the recipients for one final confirmation first.
3. Send in batches of 20 or fewer to avoid spam filters.
4. Creators with no email: write a short DM version (≤ 300 characters) for the user to send on the platform.
5. After sending: Stage = `已联系 Contacted`; record First Email and Thread Link; Next Action Date = today + 4 days.

## Stage 5 — Follow up

On every run, find Pipeline rows with `Next Action Date` ≤ today and search the mailbox for new replies.
- **Cadence:** follow-up 1 at +4 days after the first email; follow-up 2 at +5 days more (last one, light tone, "no worries if it's not a fit"). No reply after 2 → Stage = `无回复 No Response`; don't contact again for 90 days.
- Reply **in the original thread**, as a draft by default.
- **When someone replies:** summarize their intent (accept, quote, question, decline), draft a suggested response and update the Stage.

## Stage 6 — Manage the collaboration

- **Negotiate:** compare the quote with the budget, market rates for the tier and the creator's score. Suggest levers (bundle several posts, lower flat fee plus commission). Record Agreed Fee and Deal Type.
- **Brief (after confirmation):** one page with key selling points, must-include / must-avoid, sample talking points, CTA, tracking link and promo code, deliverables and due dates, review process, usage rights, and disclosure requirements (#ad / paid-partnership labels per platform and local law).
- **Tracking:** give each creator a UTM link: `utm_source={platform}&utm_medium=influencer&utm_campaign={campaign_id}&utm_content={handle}`. If a Shopify store is connected, create a unique discount code per creator.
- **Seeding:** collect the shipping address (store it in the Pipeline row only, never in the Creator Library), record the tracking number, Stage = `已寄样 Shipped`.
- **Reminders:** 3 days before a due date, draft a friendly reminder.
- **Content review:** check the draft against the brief and list the changes needed.
- **Payment:** after the post is live and verified, Stage = `已结算 Paid`.

## Stage 7 — Track performance

- Collect post metrics at **day 3, 7 and 30** after posting: views, likes, comments, shares via Apify or the browser, or from screenshots the creator sends.
- Conversions: UTM clicks and conversions from GA4 or the user's analytics; promo-code orders from Shopify if connected.
- Calculate: **CPM** = cost ÷ views × 1000; **CPE** = cost ÷ engagements; **CPA** = cost ÷ conversions; **ROAS** = revenue ÷ cost. For gifting, count product cost + shipping as cost.
- Campaign summary: total spend, reach, engagement, conversions, ROAS, top 5 and bottom 5 creators, and the best-performing angles and hooks. If the user wants charts, use the agent's data-visualization guidance if it has any.

## Stage 8 — Review & re-collab

- Update Creators from the results: historical ROI, number of collabs, Tier (up to A if strong, down to C if weak), and notes on responsiveness, cooperation and punctuality. Breach of contract or policy-violating content → blacklist with the reason.
- Tier A creators: draft a re-collab invitation that cites last campaign's results and proposes a long-term deal, annual contract or higher commission.
- Strong content: suggest the user request usage rights for paid amplification (Spark Ads / Partnership Ads).
- Campaign Stage = `已复盘 Closed`; close all its Pipeline rows.

## Stage 9 — Library upkeep (every run)

- Merge duplicate creators.
- Refresh follower and view data older than 90 days before using it.
- Report in one line: total creators in the library, count per Tier, and how many were added this run.

## Default run

When the user just says "run the influencer campaign" or similar:
1. Read `_Config` and Pipeline.
2. List today's to-dos: follow-ups due, new replies, upcoming due dates, metrics to collect.
3. Ask which ones to handle (multi-select), then do them one by one.

## Guardrails

- **Never send an email, place an order or make a payment without the user's explicit confirmation.** Create drafts by default.
- Collect public data only; respect platform terms; don't scrape private accounts. Home addresses and phone numbers stay in the Pipeline table and are never shared.
- Before scaling up any Apify run, state the expected number of results and the approximate cost.
- Every email includes an opt-out line. When a creator says no, mark it immediately and don't contact them again.
- Never invent creator data, performance numbers or rates. Leave unknown fields blank and note why.
- Read the latest version of the database before writing; only append or update the relevant rows, never overwrite whole tables.
- End each stage with one or two sentences on the result and the next step, plus the database link.

## Reference files

Read these when the stage needs them:
- `references/setup.md` — database backends, creator data sources, and how to install each one
- `references/schema.md` — the five tables and the Pipeline stage state machine
- `references/scoring.md` — scoring rubric, engagement benchmarks, fake-follower signals
- `references/outreach.md` — email rules, EN / ZH templates, DM version
