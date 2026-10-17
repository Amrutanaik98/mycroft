# Exercise 3, Chapter 3: The Verified Finance Data Contract
## Applied to Vendor Intelligence Platform

**Company:** Anthropic  
**Date:** 2026-07-13  
**Exercise:** Build a sources-of-truth ranking and data contracts for decision-moving vendor metrics.

---

## Part A: Sources-of-Truth Ranking

Ranked by authority and defensibility for vendor intelligence decisions.

| Rank | Source | Type | Status | Why |
|---|---|---|---|---|
| 1 | SEC EDGAR (10-K, 10-Q, 8-K filings) | Source of truth | Authoritative | Legally required disclosure; audited; version-controlled; CIK + accession number + period definitive |
| 2 | GitHub API (stars, commits, releases via api.github.com) | Source of truth | Authoritative | Deterministic historical records; timestamp-indexed; queryable by user/org; no human interpretation needed |
| 3 | ArXiv API (papers indexed by arxiv.org; query by author/affiliation) | Source of truth | Authoritative | Persistent identifiers; publication metadata definitive; cross-checked against company author lists |
| 4 | Named options-chain provider (e.g., IB, TD, Deribit for crypto) | Source of truth | Authoritative | Real market prices; timestamp-indexed; expiration windows definitive |
| 5 | Company investor page (crunchbase.com, pitchbook.com) | Context only | Provisional | Crowd-sourced; updated asynchronously; useful for context but not authoritative for funding claims |
| 6 | News RSS feeds + LLM classification (TechCrunch, VentureBeat, etc.) | Context only | Provisional | Lag to official announcement; sentiment classification is model judgment, not fact; useful for signal but not authority |
| 7 | LinkedIn (executive profiles, job posts) | Context only | Provisional | User-curated; not a company system of record; useful for context on personnel changes but not authoritative without independent confirmation |
| 8 | Company tweets/press releases | Not a source | Non-authoritative | Marketing claim, not verified externally; no version control; retroactively deletable |
| 9 | AI-generated summary or narrative | Not a source | Non-authoritative | Transformation of evidence, not evidence itself; no provenance; fluent output does not substitute for source |

---

## Part B: Data Contracts for Vendor Intelligence Metrics

For company: **Anthropic**  
Period: **Q3 2026 (2026-07-01 to 2026-09-30)**  
Entity: **Anthropic (parent + any consolidated subs)**  
Owner: **User (decision-maker)**  
Freshness need: **Daily for operational signals; quarterly for financial signals**

### Metric 1: Funding Status & Burn Rate (Financial Health Score)

| Field | Value | Notes |
|---|---|---|
| **Metric** | Total raised (USD) + implied runway (months) |
| **Source-of-truth** | SEC EDGAR (10-Q filing) or company investor relations + Series D announcement |
| **Period** | Q3 2026 filing (most recent 10-Q covering Q3 end date) |
| **Entity** | Anthropic consolidated |
| **Version/Accession** | LEAVE BLANK — must retrieve against EDGAR directly; e.g., 0001550152-26-XXXXXX |
| **Owner** | Company CFO + SEC filing authority |
| **Freshness need** | Q3 10-Q filed within 45 days of quarter end (by ~2026-11-15) |
| **Retrieval timestamp** | Date you pulled the 10-Q: [FILL BY HAND] |
| **Control total** | Series D amount ($5B publicly claimed) should match or exceed any disclosed capital raised |
| **Validated?** | [HUMAN TO FILL] — confirm 10-Q accession number + filing date before trusting |

**Caveats:**
- If 10-Q not yet filed, use latest 10-K (annual, staler data).
- Runway is a model judgment (burn rate / cash on hand); filing shows cash only.
- Non-public companies may not file; fallback to news announcement + investor confirmation.

---

### Metric 2: Technology Momentum (GitHub Activity)

| Field | Value | Notes |
|---|---|---|
| **Metric** | Commits/week + releases/quarter + contributor growth |
| **Source-of-truth** | GitHub API query (api.github.com/repos/anthropics/anthropic-sdk-py/events, etc.) |
| **Period** | Last 13 weeks (rolling, as of retrieval date) |
| **Entity** | Anthropic primary org repos (anthropics/) |
| **Version/Accession** | GitHub API version 2022-11-28; repo names: anthropic-sdk-py, anthropic-sdk-js, others from org list |
| **Owner** | GitHub (Anthropic's engineers execute; no human intermediary) |
| **Freshness need** | Weekly (daily if monitoring real-time velocity) |
| **Retrieval timestamp** | Date you queried the API: [FILL BY HAND] |
| **Control total** | Sum of commits across all repos in the Anthropic org must match GitHub activity feed |
| **Validated?** | [HUMAN TO FILL] — confirm you pulled from anthropics/ org and repo list is complete |

**Caveats:**
- Public repos only; private repos not visible via API.
- Commit count reflects engineering output, not feature completeness.
- Seasonal variation (holidays, release cycles) can create false signals.
- Contributors can be external (open-source); distinguish staff vs. external.

---

### Metric 3: Research Output (ArXiv Affiliation)

| Field | Value | Notes |
|---|---|---|
| **Metric** | Papers authored by Anthropic affiliates (last 6 months) + citation velocity |
| **Source-of-truth** | ArXiv API query (arxiv.org/api) filtered by author affiliation = "Anthropic" |
| **Period** | Last 6 months (180 days prior to retrieval date) |
| **Entity** | Anthropic as author affiliation string (exact string: verify from recent paper) |
| **Version/Accession** | ArXiv API v0.1; search query: all:anthropic (papers, not comments) |
| **Owner** | ArXiv (Anthropic researchers self-publish; no external gating) |
| **Freshness need** | Weekly (papers lag publication; 1-3 weeks from submission to index) |
| **Retrieval timestamp** | Date you queried ArXiv: [FILL BY HAND] |
| **Control total** | Count of papers with Anthropic affiliation must tie to company publication timeline (e.g., cross-check against company blog announcements) |
| **Validated?** | [HUMAN TO FILL] — confirm affiliation string matches recent papers + count is complete for period |

**Caveats:**
- Self-reported affiliation; external researchers may cite Anthropic incorrectly.
- Publication lag (1-3 weeks between submission and ArXiv indexing); recent papers may not yet be visible.
- Citation count lags publication (6-12 months); not available for recent papers.
- Quality of research is not measured by count (model judgment, not data).

---

### Metric 4: News & Sentiment (Classified Signals)

| Field | Value | Notes |
|---|---|---|
| **Metric** | News mentions + LLM-classified sentiment (positive/neutral/negative) |
| **Source-of-truth** | RSS feeds (TechCrunch, VentureBeat, Hacker News; list in /sources.txt) + web scrape + classification log |
| **Period** | Last 30 days (or last 60 days for trend context) |
| **Entity** | Anthropic company mentions (filter: exclude investor news, community noise) |
| **Version/Accession** | Classification model: [FILL BY HAND] (e.g., Claude 3.5 Sonnet, inference date) |
| **Owner** | News source (journalist) + classification owner (you or classification pipeline) |
| **Freshness need** | Daily during monitoring; weekly for brief |
| **Retrieval timestamp** | Date you ran scrape + classification: [FILL BY HAND] |
| **Control total** | Number of headlines retrieved must tie to RSS feed count + manual search confirmation (spot-check 5 headlines independently) |
| **Validated?** | [HUMAN TO FILL] — spot-check 5 headlines: do they mention Anthropic? Does sentiment classification match the article tone? |

**Caveats:**
- Sentiment classification is model judgment, not data; mark explicitly in the brief.
- RSS feed lag (1-2 days between publication and feed pickup).
- Exclusion filter (investor news, community noise) is subjective; list the rules.
- Absence of news is not evidence of absence (lack of coverage is not a signal).

---

### Metric 5: Personnel Stability (Executive Changes)

| Field | Value | Notes |
|---|---|---|
| **Metric** | Executive departures (C-level, board) + hiring announcements (last 12 months) |
| **Source-of-truth** | News sources (TechCrunch, LinkedIn, company press) + LinkedIn API (employment history) |
| **Period** | Last 12 months |
| **Entity** | Anthropic Inc. (US) + any consolidated entities |
| **Version/Accession** | LinkedIn profile URLs or news article URLs (cite specific URL for each executive change) |
| **Owner** | News source + LinkedIn (user-curated) + company (official announcements) |
| **Freshness need** | Monthly (or immediate upon public announcement) |
| **Retrieval timestamp** | Date you checked LinkedIn + searched news: [FILL BY HAND] |
| **Control total** | Cross-check major departures against company news/press release; confirm via LinkedIn profile update date |
| **Validated?** | [HUMAN TO FILL] — for each departure, verify against company announcement or LinkedIn change date + confirm role |

**Caveats:**
- LinkedIn is user-curated; departure date may lag actual departure.
- News coverage varies (major executives covered; mid-level departures may not be public).
- Hiring announcements are often delayed; absence of announcement is not absence of hiring.
- Context matters: departures during restructuring vs. competitive departure carry different weight.

---

### Metric 6: Competitive Position (Neo4j Graph Relationships)

| Field | Value | Notes |
|---|---|---|
| **Metric** | Direct competitors (COMPETES_WITH edges) + investor overlap (BACKED_BY edges) |
| **Source-of-truth** | Neo4j database (your production instance) |
| **Period** | Snapshot as of retrieval date (graph updated quarterly) |
| **Entity** | Anthropic node (must exist; id: verify in query) |
| **Version/Accession** | Neo4j database version: [FILL BY HAND]; query timestamp: [FILL BY HAND] |
| **Owner** | You (graph maintainer); data sourced from Crunchbase, PitchBook, news (mark each edge source) |
| **Freshness need** | Quarterly (or on major relationship change) |
| **Retrieval timestamp** | Date you queried the graph: [FILL BY HAND] |
| **Control total** | Manually verify 3-5 competitor edges: do they actually compete? Manually verify 2-3 investor edges: do they actually back both companies? |
| **Validated?** | [HUMAN TO FILL] — check 5 edges for accuracy before using the graph in the brief |

**Caveats:**
- "Competes with" is subjective; no single definition. Define your rule (e.g., "same product category + targeting same customers").
- Investor overlap may reflect financial engineering, not strategic alignment.
- Graph lag: new investors may not be in the graph for weeks.
- Private company relationships often not public.

---

## Assessment & Adequacy

### Completeness Check
- ✓ EDGAR filing: covers public financial signals only (Anthropic is private; 10-Q may not exist; fallback to news)
- ✓ GitHub: covers public repos only (private development not visible)
- ✓ ArXiv: covers published research only (unpublished work not visible; publication lag 1-3 weeks)
- ✓ News: covers publicly announced signals only (strategic moves may be private until announced)
- ✓ LinkedIn: covers disclosed executive changes only (private departures may not be public)
- ✗ **Gap**: No access to board composition, investor meetings, or strategic partnerships not yet public

### Freshness Check
- Financial metrics: quarterly (Q3 10-Q lag ~45 days; briefing may use Q2 data if Q3 not yet filed)
- GitHub activity: real-time (queryable daily; brief uses rolling 13-week snapshot)
- Research: weekly (publication lag ~2 weeks; brief may miss very recent papers)
- News: daily (RSS lag ~1-2 days; brief uses last 30-day snapshot)
- Personnel: monthly (LinkedIn lag ~1-2 weeks; public announcements vary)
- Competitive graph: quarterly (maintained quarterly; may be stale between updates)

### Sources-of-Truth Summary
**Defensible:** EDGAR, GitHub API, ArXiv API, named options chains (if company trades options).  
**Provisional:** News + classification, LinkedIn, Crunchbase.  
**Not sources:** AI-generated narratives, tweets, unverified rumors.

---

## Validation Checklist Before Brief

Before the vendor intelligence brief goes to procurement:

- [ ] Each metric is tied to a source-of-truth (not "the model said")
- [ ] Retrieval timestamps are recorded (when you pulled EDGAR, queried GitHub, etc.)
- [ ] Control totals are spot-checked (5-10% sample of data tied back to source)
- [ ] Freshness is adequate for the decision (financial is 45 days old okay? GitHub velocity is 2 weeks old okay?)
- [ ] Caveats are named in the brief (no EDGAR yet → 10-Q lag disclosed; GitHub is public-only → private development not visible)
- [ ] No metric is sourced from "the model" alone
- [ ] Every number in the brief traces to a source you can show
- [ ] Every signal is labeled as "confirmed," "suggests," "cannot claim," or "needs review" per Chapter 5 discipline

---

## Next Steps

1. **Retrieve the filings** — Manually pull the 10-Q from EDGAR or use the Edgar API.
2. **Run the GitHub query** — Pull the last 13 weeks of commit history from anthropics/ org.
3. **Query ArXiv** — Search for Anthropic-affiliated papers in the last 6 months.
4. **Aggregate news** — Run RSS feeds + scrape the last 30 days of coverage.
5. **Spot-check the graph** — Query Neo4j for Anthropic competitors; manually verify 5 edges.
6. **Fill the data contracts** — Populate retrieval timestamps, control totals, validation status.
7. **Gate decision** — Does the evidence feel adequate for a procurement recommendation?

If any source is inaccessible (e.g., EDGAR if Anthropic is private), document the gap in the brief and use the next-best source with a caveat.
