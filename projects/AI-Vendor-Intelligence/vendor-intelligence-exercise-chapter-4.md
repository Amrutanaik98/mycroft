# Exercise 3, Chapter 4: Two Customers
## Applied to Vendor Intelligence Platform

**Company:** Anthropic  
**Date:** 2026-07-13  
**Exercise:** Build an agent recipe (machine-readable preparation) and a human card (human-readable judgment) for the vendor intelligence brief.

---

## Document 1: AGENT RECIPE (Machine Customer)

### Purpose
Deterministically gather signals for vendor intelligence from four sources (EDGAR, GitHub, ArXiv, news RSS + classification), compute trend summaries, check control totals, flag missing data, and produce a structured JSON output ready for supervisor agent routing. Recipe halts at the gate before any causal interpretation or recommendation.

---

### Inputs (Specified by Path, Version, Period)

| Input | Source | Version/Accession | Period | Control |
|---|---|---|---|---|
| SEC filing | EDGAR API or manual 10-Q pull | 10-Q accession number (if public) or n/a if private | Q3 2026 (2026-07-01 to 2026-09-30) | Verify against filing date |
| GitHub signals | anthropics/ org via GitHub API v2022-11-28 | Repos: anthropic-sdk-py, anthropic-sdk-js, anthropic-cookbook, other public | Last 13 weeks prior to recipe run date | Commit count ties to activity feed |
| ArXiv signals | arxiv.org API / search all:anthropic | API v0.1; papers (not comments) | Last 6 months prior to recipe run date | Paper count ties to metadata query |
| News signals | RSS feeds (TechCrunch, VentureBeat, Hacker News) + manual search | Pull date: recipe execution date | Last 30 days prior to recipe run date | Headline count ties to feed + search |
| Neo4j graph | Production instance (your server) | Query timestamp: recipe execution date | Snapshot as of query date (quarterly refresh cadence) | Competitor count ties to manual spot-check |

---

### Deterministic Steps (The Preparation Layer)

**Step 1: Fetch EDGAR (if public) / News announcement (if private)**
```
IF company is public:
  - Query EDGAR API: latest 10-Q for ticker
  - Parse: revenue, cash, funding disclosures
  - Extract: filing date, period covered, CIK
ELSE:
  - Search news for Series X announcement
  - Extract: round size, date, investor list
END
Output: {source_file, filing_date, period, revenue, cash, funding_amount, investors}
```

**Step 2: Query GitHub API (13-week rolling window)**
```
FOR EACH repo in anthropics/org:
  - Query: commits, releases, contributors (last 13 weeks)
  - Compute: commits/week average, release count, contributor growth
  - Store: repo_name, commit_count, release_count, unique_contributors, date_range
END
Aggregate: sum commits across all repos, avg commits/week
Output: {total_commits, commits_per_week, releases, contributor_count, repos_included}
```

**Step 3: Query ArXiv (6-month rolling window)**
```
Query ArXiv API: all:anthropic
Filter: papers only (exclude comments), last 6 months
For each paper:
  - Extract: title, arxiv_id, authors, publication_date, affiliation
  - Check: "Anthropic" in author_affiliation (exact string match)
Aggregate: paper count, date range
Output: {paper_count, oldest_paper_date, newest_paper_date, papers_list}
```

**Step 4: Scrape & Classify News (30-day window)**
```
FOR EACH RSS feed in {TechCrunch, VentureBeat, Hacker News}:
  - Fetch latest 30 days of entries
  - Filter: title contains "Anthropic" OR description contains "Anthropic"
  - Extract: headline, url, publish_date, source
END
FOR EACH headline:
  - Classify sentiment: {positive, neutral, negative} (use fixed LLM prompt)
  - Extract: keywords (product, partnership, news type)
Aggregate: headline_count, sentiment_distribution
Output: {headlines, sentiment_counts, source_breakdown}
```

**Step 5: Query Neo4j Graph**
```
MATCH (anthropic:Company {name: "Anthropic"})
MATCH (anthropic)-[:COMPETES_WITH]->(competitor:Company)
MATCH (investor:Investor)-[:BACKED_BY]-(anthropic)
Return: [competitors], [investors]
Verify: manual spot-check 5 edges for accuracy
Output: {competitors, investor_list, edge_count}
```

---

### Output Schema (What Valid Output Looks Like)

```json
{
  "company": "Anthropic",
  "run_date": "2026-07-09T14:32:00Z",
  "period": "2026-07-01 to 2026-09-30",
  "financial_signals": {
    "source": "EDGAR or news announcement",
    "filing_date": "2026-11-14 (example: Q3 10-Q)",
    "total_funding_usd": 5000000000,
    "cash_on_hand_usd": 2800000000,
    "burn_rate_monthly_usd": null,
    "control_total_source": "SEC filing or investor announcement",
    "notes": "If private: Series D $5B from news; no quarterly filing"
  },
  "technology_signals": {
    "github_org": "anthropics",
    "period": "2026-06-26 to 2026-09-09 (13 weeks)",
    "repos_included": ["anthropic-sdk-py", "anthropic-sdk-js", "anthropic-cookbook"],
    "total_commits": 892,
    "commits_per_week": 68.6,
    "releases": 12,
    "unique_contributors": 24,
    "control_total": "892 commits matches API query sum: ✓"
  },
  "research_signals": {
    "source": "arxiv.org",
    "period": "2026-01-13 to 2026-07-09 (6 months)",
    "paper_count": 7,
    "papers": [
      {"title": "...", "arxiv_id": "2406.xxxxx", "pub_date": "2026-06-15"}
    ],
    "control_total": "7 papers matches ArXiv query count: ✓"
  },
  "news_signals": {
    "sources": ["TechCrunch", "VentureBeat", "Hacker News"],
    "period": "2026-06-09 to 2026-07-09 (30 days)",
    "headline_count": 14,
    "sentiment_distribution": {"positive": 8, "neutral": 5, "negative": 1},
    "headlines": [
      {"date": "2026-07-05", "title": "...", "url": "...", "sentiment": "positive"}
    ],
    "classification_model": "Claude 3.5 Sonnet",
    "control_total": "14 headlines spot-checked against 5 random RSS items: ✓"
  },
  "competitive_signals": {
    "source": "Neo4j",
    "query_date": "2026-07-09T14:32:00Z",
    "competitors": ["OpenAI", "DeepSeek", "xAI", "Meta AI"],
    "investors": ["Google", "Salesforce", "Zoom", "others"],
    "graph_refresh_date": "2026-07-01 (quarterly)",
    "spot_check_status": "5 edges manually verified; all accurate"
  },
  "flags": [
    "CONTROL_TOTAL_OK: all control totals tie",
    "FRESHNESS_WARNING: GitHub is real-time; ArXiv lag ~2 weeks; news lag ~1 day",
    "COMPLETENESS_NOTE: GitHub covers public repos only; private development not visible",
    "STOP: hand to human before interpretation"
  ]
}
```

---

### Stop Conditions (When Recipe Halts & Waits for Human)

1. **Missing source file**
   - EDGAR filing not found (company is private, Q3 10-Q not yet filed)
   - **Action**: Log and continue with fallback (news announcement)

2. **Control total mismatch**
   - GitHub commit count from API query does not match repo activity feed
   - ArXiv paper count does not match metadata query
   - **Action**: Flag and halt; investigate data integrity

3. **Completeness gap**
   - GitHub query returns 0 repos in anthropics/ org (org name wrong or archived)
   - ArXiv query returns 0 papers (affiliation string wrong)
   - **Action**: Halt and notify human to verify source access

4. **Period mismatch**
   - Filing period does not match 2026-Q3; appears to be older quarter
   - ArXiv papers are all older than 6 months
   - **Action**: Flag and halt; verify period definition

5. **High-risk signal**
   - News mentions bankruptcy, major lawsuit, or executive exodus (>3 C-level departures same week)
   - **Action**: Flag as NEEDS_HUMAN_REVIEW; do not roll into trend summary

---

### Run Log (Machine-Readable Record)

```json
{
  "recipe_name": "vendor-intelligence-brief",
  "run_id": "vi-anthropic-20260709-001",
  "execution_date": "2026-07-09T14:32:00Z",
  "inputs": {
    "company": "Anthropic",
    "period": "2026-07-01 to 2026-09-30",
    "sources": ["EDGAR", "GitHub API", "ArXiv API", "news RSS", "Neo4j"]
  },
  "execution_log": [
    {"step": 1, "task": "fetch_edgar", "status": "skipped", "reason": "company is private; no EDGAR filing"},
    {"step": 1b, "task": "fetch_news_funding", "status": "success", "result": "Series D $5B from TechCrunch 2024-04-09"},
    {"step": 2, "task": "github_query", "status": "success", "repos_found": 3, "commits": 892},
    {"step": 3, "task": "arxiv_query", "status": "success", "papers_found": 7},
    {"step": 4, "task": "news_classify", "status": "success", "headlines": 14},
    {"step": 5, "task": "neo4j_query", "status": "success", "competitors": 4, "investors": 8}
  ],
  "control_totals": {
    "github_commits": "892 commits from API = 892 commits in aggregate: ✓",
    "arxiv_papers": "7 papers from query = 7 papers in list: ✓",
    "news_headlines": "14 headlines from feeds + search spot-checked: ✓",
    "neo4j_edges": "5 edges manually verified: ✓"
  },
  "flags": [
    "COMPLETENESS_OK: all defined sources queried",
    "FRESHNESS_OK: timestamps within expected ranges",
    "FRESHNESS_WARNING: ArXiv lag ~2 weeks; very recent papers may not be indexed",
    "STOP: all signals gathered; hand to human for interpretation"
  ],
  "next_step": "human_review",
  "approver_needed": true,
  "approval_gate": "human-card analyst must review signals and write synthesis + call"
}
```

---

## Document 2: HUMAN CARD (Human Customer)

### Purpose
A vendor intelligence brief for Anthropic that answers: Is this company a stable vendor for our procurement, or are there execution/financial/competitive risks that require closer monitoring or a decision to diversify?

---

### Evidence Summary
**[LEAVE BLANK — filled only after recipe runs and human reviews the signals]**

Template:
- Financial: Anthropic has [funding amount] raised, [cash balance] on hand, implied runway of [X months]. Series D ($5B) signals investor confidence but runway depends on burn rate (not public). [Confidence level].
- Technology: [GitHub commits/week], [releases], [contributors] signal [velocity level]. ArXiv shows [paper count] research papers in 6 months, indicating [research momentum]. [Confidence level].
- News: [Headline count], sentiment distribution [pos/neutral/neg]. Key signals: [partnership, product launch, regulatory, personnel]. [Confidence level].
- Personnel: [Executive changes in last 12 months]. [Confidence level].
- Competitive: [Direct competitors], [investor overlap]. [Positioning relative to tier].

**Do not write this section until the recipe has run and you have reviewed the actual signals.**

---

### Caveats (What the Recipe Did NOT Check)

- [ ] **No access to private financial data.** Funding and runway are inferred from news; actual burn rate and quarterly cash are private. Assumption: company is solvent for next 12 months based on $5B raised (unverified).
- [ ] **GitHub activity does not imply product roadmap.** High commit velocity could reflect refactoring, test infrastructure, or internal tools, not feature delivery. No product roadmap visible.
- [ ] **News coverage is public announcements only.** Strategic partnerships not yet announced are not visible. Absence of news is not absence of activity.
- [ ] **Research papers lag publication by 1-3 weeks.** Very recent research may not yet be indexed on ArXiv.
- [ ] **Personnel data from LinkedIn is self-reported.** Departure dates may lag actual departure; mid-level departures may not be public.
- [ ] **Competitive graph is maintainer's judgment** (your judgment). Definition of "competes with" is subjective. Not authoritative until you verify edges.
- [ ] **No access to customer relationships, contracts, or revenue composition.** Brief cannot assess customer concentration or churn risk.
- [ ] **No access to board decisions or strategic direction.** Brief is backward-looking (recent signals); forward direction is a business judgment.

---

### Decisions: What Must Happen Before Recommendation

- [ ] **Control totals verified.** Analyst confirms: GitHub commits tie to API query (✓); ArXiv papers match metadata (✓); news headlines spot-checked (✓); Neo4j edges manually verified (5 sample checked, ✓).
- [ ] **Freshness adequate for decision.** Analyst confirms: Financial data lag (45 days) acceptable? GitHub lag (real-time) acceptable? News lag (1 day) acceptable? If any gap is unacceptable, source is not ready.
- [ ] **Caveats reviewed.** Analyst confirms: Does absence of private financial data create unacceptable risk for procurement decision? Can we proceed on news-derived funding assumptions?
- [ ] **Contradictions resolved.** If any two sources tell different stories (e.g., news says layoffs; GitHub shows high velocity), analyst reconciles before proceeding.
- [ ] **"No go" signals addressed.** If recipe flagged any NEEDS_HUMAN_REVIEW item (bankruptcy claim, major lawsuit, 3+ C-level departures), analyst investigates and either clears or escalates.

---

### Open Questions (Analyst to Address)

1. **Burn rate.** We know Anthropic raised $5B (Series D) but have no public burn-rate data. How quickly is that capital being consumed? Runway assumption of 24+ months is a guess. Can we confirm with industry comparables or investor calls?

2. **Product-market fit.** GitHub velocity and ArXiv research are activity signals, not market fit signals. Are users adopting the product, or is the activity internal? Revenue/user-acquisition data needed but not public.

3. **Personnel risk.** High-profile departures (CTO, board member) might indicate tension. News captures some changes; LinkedIn is user-reported. How confident are we that we're seeing the full picture?

4. **Regulatory risk.** News signals 0 regulatory filings so far (as of July 2026). But AI regulation is rapidly changing. Is absence of news a signal that Anthropic is not under regulatory pressure, or that pressure is not yet public?

5. **Competitive timing.** OpenAI released GPT-5, DeepSeek released new model. Is Anthropic's research velocity sufficient to keep pace? ArXiv papers are a leading indicator but not a complete picture of product roadmap.

---

### Gate
**Analyst (you) sign-off before brief leaves desk:**

- [ ] Reviewed all signals from the recipe run log
- [ ] Spot-checked control totals (at least 5 samples per source)
- [ ] Assessed whether caveats are acceptable for the procurement decision
- [ ] Resolved any contradictions or "needs review" flags
- [ ] Wrote evidence summary (no conjecture; only what signals directly support)
- [ ] Used warranted verbs: "confirms" only where evidence is complete; "suggests" where pattern is consistent but causal story is uncertain; "cannot claim" where evidence is insufficient
- [ ] Left decision blank until gate review (approval below)
- [ ] **Analyst name & date:** [YOUR NAME, DATE]

**Procurement approver sign-off:**

- [ ] Read the evidence summary
- [ ] Confirmed the caveats are acceptable for the buy/hold/diversify decision
- [ ] Approved the brief for forwarding to vendor decision committee
- [ ] **Approver name & date:** [PROCUREMENT OWNER, DATE]

---

## Process Notes for the Gate

**Before you write the brief:**
1. Recipe runs → produces JSON output + run log
2. You review the run log: all control totals ✓? Any stop conditions triggered? Any flags?
3. You fill the evidence summary by reading the recipe output (not by narrative; by data)
4. You assess the caveats: is absence of private financial data okay for this decision? (Yes = proceed; No = wait for more data)
5. You resolve any contradictions (if news says layoff but GitHub shows high velocity, why?)
6. You write the brief with warranted verbs calibrated to evidence
7. You sign off at the gate
8. Procurement approver reviews and signs off
9. Brief goes to vendor decision committee

**The recipe does not write the recommendation.** The recipe prepares the surface. You decide.

---

## Next Steps

1. Run the recipe against Anthropic's sources (EDGAR/news, GitHub, ArXiv, news RSS, Neo4j)
2. Receive the JSON output + run log
3. Review control totals and flags
4. Fill the caveats and open questions based on what you found
5. Write the evidence summary (no spin; just what the signals show)
6. Gate review: do the signals answer "is this vendor stable?" adequately?
7. Decision: [your call; do not fill until gate review is complete]
