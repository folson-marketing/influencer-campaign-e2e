# Brand scan

Run this whenever the user gives a brand name: their own brand, or a competitor. The goal is to find **every creator and post related to that brand** that can be found through public data, and save them all to the database.

## 1. Build the search terms

Before searching, list the terms and show them to the user:
- Brand name, including common spellings and spacing variants (e.g. "Glow Lab", "GlowLab", "glowlab")
- The brand's official handles on each platform (`@glowlab`) — find them first if the user doesn't give them
- Branded hashtags (`#glowlab`, `#glowlabpartner`, campaign hashtags)
- Hero product names
- Discount-code and affiliate patterns (e.g. "code GLOW", the brand's affiliate / storefront domain)

Ask the user to add anything missing (old brand names, campaign hashtags, product names).

## 2. Where to look

Cover each platform in the brief (default: TikTok, Instagram, YouTube):

| Signal | Where to find it |
|---|---|
| Paid partnership label | Posts marked "Paid partnership with {brand}" / branded content |
| Tagged posts | The brand account's tagged / mentions tab |
| @mentions | Captions and descriptions mentioning the brand handle |
| Branded hashtags | Hashtag pages and hashtag scrapers |
| Keyword mentions | Platform search, caption / title / description search, video transcripts where available |
| Discount codes / affiliate links | Captions, descriptions, link-in-bio pages |
| Brand's own reposts | Creators the brand reposts or features on its own account |

Use Apify actors (profile, hashtag, search and post scrapers) or the browser, following the sourcing rules in `SKILL.md`: state the actor and search terms before each run, run a small batch first, and give a cost estimate before the full sweep.

## 3. Search until results run out

- Page through each search until no new posts come back, instead of stopping at the first page.
- Default time window: the last 12 months. Ask the user if they want a longer one.
- Deduplicate posts by URL and creators by platform + handle.
- **Be honest about coverage.** Platform search never returns literally everything, so "all" means everything these sources returned. Report what was searched, how many results each search returned, and any limits hit (private accounts, deleted posts, rate limits, platforms with no working actor).

## 4. Classify each post

Set `Mention Type` for every post:
- **Paid partnership**: platform paid-partnership label, #ad / #sponsored, or disclosed sponsorship
- **Affiliate**: discount code or affiliate link, no paid label
- **Gifted**: "gifted" / "PR package" disclosure
- **Organic**: mentions or tags the brand with no sign of a deal
- **Brand repost**: featured on the brand's own account

If the evidence is unclear, use the closest type and set `Data Confidence` = Estimated.

## 5. Save to the database

- **Brand Mentions table:** one row per post (fields in `references/schema.md`).
- **Creators table:** add or update every creator found (dedupe on platform + handle). Add tags `brand:{brand}` and `{mention type}:{brand}` (e.g. `paid:glowlab`), and fill `Source` and `Search Query`.
- **Pipeline:** if a campaign is active, ask the user whether to add these creators to it as `Sourced`. Don't add them automatically.
- Follow the data-integrity rules: check before writing, re-read after writing.

## 6. Report to the user

- Totals: posts found, unique creators, and a breakdown by platform and by mention type.
- Top creators by reach, as profile cards; everyone else in a compact table.
- What it means:
  - **Own brand:** creators who posted organically are warm leads; past paid partners are re-collab candidates (check their performance first).
  - **Competitor:** creators who already work in the category; note who is on a paid deal now (they may be exclusive).
- The database link and a summary of what was added or updated.
