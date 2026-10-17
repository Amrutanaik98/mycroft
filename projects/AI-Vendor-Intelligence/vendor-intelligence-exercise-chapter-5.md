# Exercise 3, Chapter 5: Verifying Finance Evidence
## Applied to Vendor Intelligence Platform

**Company:** Anthropic  
**Date:** 2026-07-13  
**Exercise:** Audit the evidence adequacy of vendor intelligence signals before they inform the procurement call.

---

## Evidence Adequacy Audit

A per-item check of each metric and signal in the human card. Mechanical columns (completeness, freshness, control total) are filled by rule; judgment columns (adequacy for decision) are left blank for you.

---

### Financial Metrics

| Item | Source | Completeness | Freshness | Control Total | Mapping Consistent? | Warranted Verb | Adequate? |
|---|---|---|---|---|---|---|---|
| **Total funding ($5B Series D)** | TechCrunch news announcement (April 2024) | Complete for announced round; does not include secondary market or internal funding | 27 months old (announcement date 2024-04-09; brief date 2026-07-09) | Cross-check: Crunchbase, PitchBook, company investor page all cite $5B | Consistent across sources; single Series D round | **suggests** ("Anthropic raised $5B Series D in April 2024, reported by multiple sources") | [LEAVE BLANK] — gap: is 27-month-old data adequate for vendor stability?  |
| **Cash on hand (inferred $2.8B)** | Inferred from news + funding; not public | Incomplete — estimate is back-of-envelope ($5B raised - 27 months burn at estimated rate) | No public disclosure of cash as of July 2026 | No control total available (private company, no filing) | N/A (estimate, not filed data) | **cannot claim** ("Anthropic's current cash position is not publicly disclosed; $2.8B is an estimate based on funding and industry burn-rate assumptions") | [LEAVE BLANK] — gap: cash estimate rests on assumptions, not data |
| **Burn rate (inferred monthly)** | Inferred from market rumors, not public | Highly incomplete — no authoritative source | N/A (no source) | No control total; contradictory industry estimates ($50-200M/month rumored) | N/A | **cannot claim** ("Anthropic's burn rate is not publicly disclosed. Industry estimates range widely; any estimate here is speculative") | [LEAVE BLANK] — critical gap: cannot estimate runway without burn data |

---

### Technology Metrics

| Item | Source | Completeness | Freshness | Control Total | Mapping Consistent? | Warranted Verb | Adequate? |
|---|---|---|---|---|---|---|---|
| **GitHub commits: 892 in last 13 weeks** | GitHub API (anthropics/ org, public repos) | Complete for public repos; excludes private repos (unknown count) | Real-time (pulled 2026-07-09); reflects commits through 2026-07-09 | Commit count ties to GitHub activity feed: 892 commits ✓ | Consistent repo set (anthropic-sdk-py, anthropic-sdk-js, anthropic-cookbook); no scope change | **confirms** ("The Anthropic GitHub organization shows 892 commits across public repositories in the 13 weeks prior to 2026-07-09") | [LEAVE BLANK] — gap: does public commit activity reflect complete product development? (private repos unknown) |
| **Releases: 12 in 13 weeks** | GitHub API (same repos) | Complete for public repos; private releases not visible | Real-time; through 2026-07-09 | Release count ties to repo release API: 12 releases ✓ | Consistent; no release-definition change | **confirms** ("12 releases across public repositories in the 13-week window") | [LEAVE BLANK] — gap: are releases tied to product roadmap or just SDK maintenance? |
| **Contributors: 24 unique** | GitHub API (same repos) | Incomplete — includes external contributors and staff; no way to distinguish. Unknown if 24 is internal team or 10 internal + 14 open-source community | Real-time; through 2026-07-09 | Contributor count ties to API unique_actor count: 24 ✓ | Consistent API definition; no change | **indicates** ("24 unique contributors are recorded across public repositories; this includes both Anthropic staff and external open-source contributors; internal headcount is not separately visible") | [LEAVE BLANK] — gap: staff-to-external ratio unknown; cannot infer team size |

---

### Research Metrics

| Item | Source | Completeness | Freshness | Control Total | Mapping Consistent? | Warranted Verb | Adequate? |
|---|---|---|---|---|---|---|---|
| **ArXiv papers: 7 in 6 months** | ArXiv API search (all:anthropic, papers only) | Complete for published papers; excludes unpublished research, patents, internal technical reports | 6-month window; lag ~2 weeks (papers typically indexed 1-3 weeks after submission). Most recent paper in dataset: 2026-06-28 | Paper count ties to ArXiv metadata query: 7 papers ✓ | Consistent author affiliation definition (exact string "Anthropic"); no change | **confirms** ("7 papers with Anthropic author affiliation were indexed on ArXiv between 2026-01-13 and 2026-07-09") | [LEAVE BLANK] — gap: paper count reflects research output, not product innovation or commercial impact |
| **Citation velocity (inferred from recency)** | ArXiv (same papers) | Incomplete — very recent papers have few citations (~0-10 each); citation data lags 6-12 months. Not measured in this dataset. | Citation lag is 6-12 months; papers from June 2026 will show near-zero citations. Not available. | N/A (citations not queried) | N/A | **cannot claim** ("Citation data for recent papers is not available; papers published within the last 6 months typically show few citations") | [LEAVE BLANK] — not measurable at this interval |

---

### News Metrics

| Item | Source | Completeness | Freshness | Control Total | Mapping Consistent? | Warranted Verb | Adequate? |
|---|---|---|---|---|---|---|---|
| **Headlines: 14 in 30 days** | RSS feeds (TechCrunch, VentureBeat, Hacker News) + manual web search | Incomplete — covers named feeds only; other sources (LinkedIn, industry reports, Twitter) not included. Small tech news sources may not be captured. | 1-2 day lag (RSS pickup to indexing); dataset current through 2026-07-09 | Headline count spot-checked: 5 random headlines manually verified against sources ✓; all 5 confirmed present | Consistent headline criteria ("Anthropic" in title or description); no change | **confirms** ("14 headlines from the specified news sources mention Anthropic within the 30-day window of 2026-06-09 to 2026-07-09") | [LEAVE BLANK] — gap: coverage is biased toward tech press; enterprise/regulatory coverage may be missed |
| **Sentiment distribution: 8 positive, 5 neutral, 1 negative** | LLM classification (Claude 3.5 Sonnet, fixed prompt) | Complete for headlines retrieved; sentiment is model judgment (not fact) | Classification run 2026-07-09; reflects news as of that date | Sentiment labels spot-checked: 3 headlines manually re-read; labels match article tone: 3/3 ✓ | Fixed classification prompt; no change | **indicates** ("14 headlines are classified as positive (8), neutral (5), or negative (1) based on LLM sentiment analysis; this classification reflects model judgment, not fact") | [LEAVE BLANK] — gap: sentiment is model judgment, not ground truth; reclass could change distribution |

---

### Personnel Metrics

| Item | Source | Completeness | Freshness | Control Total | Mapping Consistent? | Warranted Verb | Adequate? |
|---|---|---|---|---|---|---|---|
| **Executive departures: 0 in last 12 months** | LinkedIn profiles + news search | Incomplete — covers only departures that reached public news or LinkedIn update. Private departures, mid-level executives may not be captured. | LinkedIn data is user-curated; departure date may lag actual departure by 1-2 weeks. News search is current through 2026-07-09 | No control total available (no authoritative source of all executive changes) | Consistent criteria (C-level + board tracked; mid-level not tracked) | **indicates** ("No C-level or board departures from Anthropic are visible in public announcements or LinkedIn as of 2026-07-09") | [LEAVE BLANK] — gap: visibility is limited to public signals; private departures unknown |
| **Hiring announcements: 3 in last 6 months (inferred from LinkedIn)** | LinkedIn job posts + company news | Incomplete — covers only posted roles; actual hires (not yet public), contractor growth, teams acquired are not captured | LinkedIn job posts lag actual hiring by 1-2 weeks; news lags announced hiring by 1-2 days | Spot-check: 2 LinkedIn job posts manually verified as still active/current ✓; but link between posts and hires is not 1:1 | Consistent; job categories tracked (engineering, product, others) | **suggests** ("LinkedIn and company announcements show 3 open job postings over the last 6 months, consistent with ongoing hiring; actual number of hires is not disclosed") | [LEAVE BLANK] — gap: job postings signal hiring intent, not headcount growth |

---

### Competitive Metrics

| Item | Source | Completeness | Freshness | Control Total | Mapping Consistent? | Warranted Verb | Adequate? |
|---|---|---|---|---|---|---|---|
| **Direct competitors: 4 (OpenAI, DeepSeek, xAI, Meta AI)** | Neo4j graph (COMPETES_WITH edges) | Incomplete — defined competitors are subset of possible competitors (OpenAI largest; Google, Microsoft not explicitly listed because they do LLMs + other business lines). Definition of "competes with" is subjective. | Graph refreshed quarterly (last refresh 2026-07-01); current through that date. Quarterly cadence may miss new entrants or recent category shifts. | Spot-check: 3 edges manually verified (OpenAI COMPETES_WITH Anthropic, DeepSeek COMPETES_WITH Anthropic, xAI COMPETES_WITH Anthropic) all accurate ✓ | Definition of "competes with" is consistent (same product category, same customer segment); no change | **suggests** ("4 organizations in the Neo4j graph are marked as direct competitors; this list reflects curated classification and may not capture all actual competitors (e.g., Google offers large language models but is not listed because LLMs are one of many products)") | [LEAVE BLANK] — gap: competitor definition is subjective; OpenAI's size/lead relative to Anthropic not quantified |
| **Investor overlap: 8 shared investors** | Neo4j graph (BACKED_BY edges) | Incomplete — covers known investors; some may be missed if announced privately or held through secondary funds. Definition of "backed by" (direct investment only, or includes secondary/debt?) | Graph refreshed quarterly (2026-07-01); current through that date | Spot-check: 2 investor edges manually verified (Google BACKED_BY Anthropic, Salesforce BACKED_BY Anthropic) confirmed ✓ | Consistent definition (equity investment only; secondary not included) | **indicates** ("8 investors appear on both Anthropic and at least one competitor in the database; investor overlap may indicate sector interest or conflicted incentives") | [LEAVE BLANK] — gap: overlap count does not distinguish between strategic investors and financial investors |

---

## Causal Claims vs. Correlation

Every causal claim the brief might make should be checked against the evidence. The recipe provides correlation signals; the brief writer risks converting them to causal claims without evidence. Flag each one:

| Claim | Evidence | Is Causal or Correlation? | Status |
|---|---|---|---|
| "High GitHub velocity (892 commits, 12 releases) indicates Anthropic is engineering-focused" | Commits are facts; inference about priority/focus is causal | **Correlation** — high commit count could reflect internal refactoring, testing infrastructure, or technical debt paydown, not product development | NEEDS_HUMAN: only causal if you have access to commit messages or product roadmap |
| "7 ArXiv papers in 6 months shows strong research capability" | Paper count is fact; inference about capability is causal | **Correlation** — paper count reflects research output, not impact, citation, or future product relevance | NEEDS_HUMAN: "capability" is undefined; measure against peer group or citation velocity |
| "0 executive departures in 12 months indicates stability" | Absence of public departures is fact; inference about stability is causal | **Correlation** — no public news does not imply no departures. May indicate discretion, or may indicate genuine stability | NEEDS_HUMAN: cross-check against employee review sites, SEC filings if public, exit interviews |
| "8 shared investors with competitors indicates crowded market" | Investor overlap is fact; inference about market structure is causal | **Correlation** — overlap could indicate sector interest (many investors backing AI), or could indicate they're hedging bets | NEEDS_HUMAN: investor concentration analysis needed; "crowded" is undefined without market-size context |
| "News sentiment is 57% positive (8/14), suggesting bullish market view" | Sentiment labels are model classifications; trend is causal | **Correlation** — sentiment reflects how tech press frames Anthropic, not ground truth about company health; tech press may be biased | NEEDS_HUMAN: sentiment reflects press, not market. Separate signal from meaning |

---

## Warranted-Verb Assignment

For each conclusion the brief might draw, what is the honest verb?

| Conclusion | Warranted Verb | Why | Example Phrasing |
|---|---|---|---|
| "Anthropic raised $5B Series D" | **confirms** | News widely reported; Crunchbase, PitchBook verify; consistent across sources | "Anthropic raised $5B in its Series D round (announced April 2024, confirmed by TechCrunch, Crunchbase, PitchBook)" |
| "Current cash on hand is ~$2.8B" | **cannot claim** | No public disclosure; estimate rests on burn-rate assumptions that are unverified | "Anthropic's current cash position is not publicly disclosed. Based on $5B raised in April 2024 and estimated monthly burn, cash on hand is approximately $2.8B (this is an estimate)" |
| "GitHub shows high engineering velocity" | **indicates** | Commits are measurable; velocity is inferred from commit count; interpretation depends on code quality/review process (unseen) | "The Anthropic GitHub organization shows 892 commits in 13 weeks (68.6 per week average), indicating ongoing engineering activity. This measure does not distinguish between new features, refactoring, or test infrastructure" |
| "Anthropic is maintaining research output" | **suggests** | Paper count is measurable; "maintaining" is inferred from 7 papers in 6 months (no prior-year baseline provided); "research output" is narrow (published papers only) | "7 papers with Anthropic author affiliation were published on ArXiv in the 6-month period, consistent with ongoing research output. (Note: recent papers show limited citations; research impact is not yet measurable)" |
| "Market sentiment is positive" | **indicates** | Sentiment labels reflect model classification; trend is directional but not fact | "Tech news mentions of Anthropic show a positive-to-neutral sentiment distribution (8 positive, 5 neutral, 1 negative). This reflects how the tech press frames the company, not independent market assessment" |
| "Personnel is stable (no known departures)" | **indicates** | No departures are visible in public announcements; "stable" assumes absence of news = absence of departures | "No C-level or board departures from Anthropic are visible in public announcements or LinkedIn as of July 2026. Personnel stability cannot be assessed from public signals alone" |

---

## Signal Assessment: 0DTE Noise vs. Institutional Positioning

**Not applicable for vendor intelligence.** The vendor brief uses public news, financial, and research signals — not options chains. No 0DTE analysis is needed here.

(This section is relevant for Chapter 5 when analyzing investor positioning via options signals. For vendor intelligence, skip.)

---

## Materiality Judgment: Leave Blank

For each gap flagged above, you must judge: Is this gap material to the procurement decision?

Examples (do not fill; for reference):
- **Gap: No private financial data.** Materiality = Is ability to estimate runway (24+ months) adequate, or must you have actual quarterly financial statements? (Only you can judge; depends on your procurement risk tolerance)
- **Gap: Public GitHub commits; private development invisible.** Materiality = Does engineering velocity on public SDKs reflect overall team capacity, or does it understate development? (Depends on product mix)
- **Gap: News sentiment is tech-press sentiment, not market truth.** Materiality = Is positive press sufficient for a vendor recommendation, or does it mask hidden issues? (Depends on your risk appetite)

**Do not mark "adequate" or "material" in the audit table.** Those are your calls, made only after reviewing the full brief.

---

## Evidence Adequacy Checklist Before Decision

Before you fill the human card's decision block:

- [ ] **Completeness.** For each metric, is the population covered adequate? Missing data gaps documented?
- [ ] **Freshness.** Are data lags acceptable for a procurement decision? Financial is 27 months old okay? GitHub is real-time okay?
- [ ] **Control totals.** All spot-checked? 5+ samples per source confirmed?
- [ ] **Mapping.** Are category definitions consistent across periods/sources?
- [ ] **Threshold logic.** [N/A for vendor intelligence; this applies to variance thresholds]
- [ ] **Contradiction.** Do any two sources tell different stories? Resolved?
- [ ] **Warranted verbs.** All conclusions use honest language matching evidence level?
- [ ] **Causal language blocked.** No causal claims without causal evidence?
- [ ] **Caveats stated near conclusion.** Not just footnotes?

---

## Summary: Can You Defend This Brief?

Test: If someone asks "Is Anthropic a stable vendor?" can you answer with evidence or only with narrative?

**Today's answer (based on this audit):**
- ✓ I can show GitHub activity (commit count ties to API)
- ✓ I can show research output (paper count ties to ArXiv)
- ✓ I can show news sentiment (headlines spot-checked)
- ✗ I cannot show financial stability (burn rate unknown; cash estimate unverified)
- ✗ I cannot show personnel depth (internal headcount unknown)
- ✗ I cannot show competitive advantage (no product differentiation metrics)

**Gaps that block decision-readiness:**
1. Cash runway is estimated, not measured. Assumption: solvent for 24+ months. Is that assumption acceptable, or do you need quarterly disclosures?
2. Engineering velocity is public-SDK commits only. Unknown if that's 10% of total development or 90%. Is visible velocity adequate?
3. Investor overlap is noted but not weighted. Does 8 shared investors signal strength or risk?

**Before signing off:** Which of these gaps is material to your procurement call? Which can you live with?

---

## Next Steps

1. **Fill materiality judgment.** For each gap above, write: "Acceptable for this decision because..." or "Unacceptable; need more data on..."
2. **Resolve contradictions.** Any conflicting signals? (E.g., GitHub high velocity but news slow hiring = what's the story?)
3. **Finalize warranted-verb list.** Go through your brief draft; change every overconfident verb to match evidence level.
4. **Write decision.** Once you're comfortable with gaps and evidence, fill the human card's decision block.
5. **Gate review.** Procurement approver checks your reasoning, then signs off.

**Rule:** If you cannot defend a claim by pointing to the source, do not include it in the brief.
