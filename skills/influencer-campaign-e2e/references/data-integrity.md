# Data integrity

These rules apply to every stage. They exist because the most damaging failures in a campaign database are silent ones: a shifted column, a made-up follower count, a dropped row.

## Before writing

- **Required fields:** every Creators row has Platform, Handle and Profile URL; every Pipeline row has Campaign ID, Creator ID and Stage.
- **Types:** follower, view and engagement counts are numbers (not "12.3K" strings); dates are real dates; URLs are complete.
- **Duplicates:** check against existing rows for the same platform + handle, and for the same email on different handles. Merge or flag; don't create a second row.
- **Where possible, validate the batch locally first** (e.g. build it as a table in Python and check it) before sending it to a live destination.

## After writing

- Read the written rows back from the destination (Google Sheets, Bitable or the file).
- Compare with what you meant to write: row count, column alignment, and a spot check of key fields (handle, followers, email, score).
- If anything is shifted, truncated, duplicated or missing, fix it before moving on and tell the user what went wrong.

## Labelling estimates

- Mark every estimated or proxy value **inline wherever it appears**, in chat and in the database: `~12,000 (est.)`.
- Set the row's `Data Confidence` field:
  - **Verified**: read directly from the platform or an official export
  - **Estimated**: calculated from partial data (e.g. average views from only 5 posts)
  - **Proxy**: stands in for a metric you couldn't get (e.g. likes used in place of views)
- When you show an engagement metric, say how it was calculated, e.g. "ER 3.4% = avg (likes + comments) on last 12 posts ÷ followers".
- Any metric calculated from an estimated input (CPM, ROAS, …) is itself estimated.

## Sanity checks

Flag these for the user instead of silently keeping or dropping them:

| Check | Flag when |
|---|---|
| Engagement rate | Above 20%, or below 0.1% with 10k+ followers |
| Views vs. followers | Average views above 10× followers, or below 1% of followers |
| Activity | Followers present but no posts in the last 60 days |
| Contact | Same email on two or more handles |
| Growth | Follower count jumped by more than 50% since the last snapshot |
| Performance | CPM or ROAS far outside the rest of the campaign (more than 10× the median) |

A flag isn't an automatic rejection: some are legitimate (a viral post, an agency email shared by several creators). Note the reason and let the user decide.

## Never silently drop data

If a row or field can't be written (a tool error, a missing column, a value that failed validation), say which one and why, and leave the field blank rather than guessing.

## Sourcing transparency

Every time new data is sourced, tell the user:
- the tool or actor used (e.g. the Apify actor name, "browser on tiktok.com/search"),
- the exact search terms, hashtags or profile URLs,
- how many results came back and how many were kept.

Record the tool in `Source` and the search terms in `Search Query`.
