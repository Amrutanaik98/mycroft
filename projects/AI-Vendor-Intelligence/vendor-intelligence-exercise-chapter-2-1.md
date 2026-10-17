# Exercise 1, Chapter 2: Vendor Intelligence Workflow Decomposition

**Project:** Your Own Mycroft — vendor intelligence brief  
**Input:** Company name (e.g., "Anthropic")  
**Output:** 6-section brief with citations  

---

## Workflow Inventory: Preparation vs. Judgment

| Step | Task | Classification | Why | AI Appropriate? |
|---|---|---|---|---|
| 1 | Pull data from EDGAR, GitHub, ArXiv, News RSS | Preparation | Mechanical data fetch against known APIs/sources; rule-based | ✓ Yes |
| 2 | Normalize signal format (deduplicate, tag source, assign occurred_date) | Preparation | Mechanical transformation; schema-driven | ✓ Yes |
| 3 | Score each signal (0-100 confidence/magnitude) | **Preparation + Judgment** | Mechanical scoring rule (if funding round detected, score = X) is prep; *deciding the score rule itself* is judgment | ✓ Prep only (use fixed rules) |
| 4 | Classify news sentiment (positive/neutral/negative) | Preparation | LLM classification against training data; repeatable | ✓ Yes (with fixed prompt) |
| 5 | Supervisor routes signals → agents by signal_type | Preparation | Rule-based routing (all `github_*` → Tech Agent) | ✓ Yes |
| 6 | **Financial Agent:** Extract funding trends, burn signals, runway | Preparation | Query signals, compute trends, cite sources | ✓ Yes |
| 7 | **Technology Agent:** Extract GitHub velocity, research output | Preparation | Count commits/releases, find ArXiv papers | ✓ Yes |
| 8 | **News Agent:** Extract partnerships, product launches, negatives | Preparation | Query classified signals, list with dates | ✓ Yes |
| 9 | **Personnel Agent:** Extract executive changes, headcount trends | Preparation | Query signals, timeline events | ✓ Yes |
| 10 | **Competitive Agent:** Find direct competitors, investor overlap | Preparation | Query Neo4j graph edges | ✓ Yes |
| 11 | Synthesize 6 sections into coherent narrative | **Judgment** | Deciding how the signals fit together; what is the story they tell? | ✗ No (human only) |
| 12 | Draft procurement recommendation (buy/hold/sell) | **Judgment** | Deciding what the brief means for procurement decision | ✗ No (human only) |
| 13 | Assess defensibility: "Can we stand behind this brief?" | **Judgment** | Reviewing whether evidence is adequate, whether anything is missing | ✗ No (human only) |
| 14 | Gate decision: approve brief before sharing with procurement | **Judgment + Approval** | Named human confirms the brief is ready | ✗ No (human only) |
| 15 | Own the brief (accountability for any errors) | Approval | Someone's name is on the line | ✗ No (human only) |

---

## Where I Disagree With the Boundaries (Human Review)

### Potential problem: Step 3 (Signal Scoring)

**Current design:** Fixed rule — "if EDGAR 10-K shows funding, score = 80; if older than 6 months, decay to 50"

**Concern:** The score rule itself is a judgment (materiality). A $5M funding round for a $100B company is different from a $5M round for a $10M startup. Should the score account for company stage? If so, that rule-making is judgment, not prep.

**Recommendation:** Separate the score rule from the scoring execution.
- Preparation: Execute the rule you were given
- Judgment: Define/review the scoring rule (who decides how stale signals decay? who weights funding stage?)

### Potential problem: Step 11 (Synthesis narrative)

**Current design:** AI agent synthesizes 6 sections → draft summary paragraph

**Concern:** The synthesis step crosses the boundary. It is not just organizing signals (prep); it is deciding what the signals *mean* (judgment). A brief that says "Personnel instability + recent layoffs = execution risk" is a causal claim, not a data summary.

**Recommendation:** Split the synthesis.
- Preparation: Organize the 6 sections with their scores and sources (table form)
- Judgment: Human reads the table and writes the integration ("Here's why these signals together suggest risk")

---

## The Assessment Artifact: Reallocation Hypothesis

**Original workflow (pre-automation):**
- Analyst: 2 hours manual data pulls + normalization
- Analyst: 1 hour formatting 6 sections  
- Analyst: 30 min writing synthesis
- Analyst: 30 min producing recommendation + gate decision
- **Total: 4 hours, mostly preparation with judgment compressed at the end**

**Reallocated workflow (with recipe automation):**
- Recipe: Data pulls, normalization, agent routing, 6-section formatting (30 min elapsed)
- **Analyst now has 3.5 hours recovered**
- Analyst: 1 hour deep-reading each of 6 sections (checking signal quality + sources)
- Analyst: 1 hour assessing defensibility (what's missing? what would change my call?)
- Analyst: 1 hour writing narrative synthesis (connecting the signals)
- Analyst: 30 min gate decision + sign-off
- **Total: 4 hours, but now 3 hours on judgment instead of <30 min**

---

## Stop Conditions: Where the Recipe Halts

The recipe must **not** cross into judgment:

1. ✓ Recipe produces 6 sections with signal tables + sources
2. ✓ Recipe flags: "X signals unvalidated" (data quality warning)
3. ✗ Recipe does **not** write the synthesis narrative
4. ✗ Recipe does **not** recommend buy/hold/sell
5. ✗ Recipe does **not** gate-approve the brief

**The gate:** Analyst reviews the 6 sections + source audit, writes synthesis + recommendation, signs off. Only then does the brief leave the desk.

---

## Scope Definition: What the Recipe is Allowed to Touch

- **Company scope:** Top 50 AI companies (defined list in neo4j, not inferred)
- **Signal types:** Only types with validation rules defined (funding, github_activity, research_paper, news, executive_change, headcount)
- **Data sources:** Only EDGAR, GitHub, ArXiv, News RSS (no custom web scraping)
- **Recency:** Signals from last 12 months only (older signals still in DB but marked stale)
- **Refresh cadence:** On-demand via CLI; no batch processing without explicit approval
- **Output destinations:** Analyst desk only (JSON file); never auto-emails or files to shared drives

---

## Verification: What Would Make the Brief Defensible?

If someone asks in 3 months: "Why did we recommend buy for Anthropic?" — what would we need to show?

| Claim in Brief | Defensible evidence | Source | Owner confirmation |
|---|---|---|---|
| "Anthropic has $5.5B funding, Series C" | EDGAR 8-K filing + press release URL | SEC EDGAR + Anthropic press | CFO/Board chair could confirm |
| "GitHub shows 140 commits/week avg" | GitHub API snapshot, dated 2026-07-09 | scripts/ingest/github-signals.py run log | GitHub data owner can re-run query |
| "ArXiv: 12 papers in last 6 months" | ArXiv query result (JSON), dated 2026-07-09 | scripts/ingest/arxiv-signals.py run log | ArXiv maintainer can re-run |
| "News: 3 partnerships announced Q2 2026" | News URL links + crawl dates | scripts/ingest/news-signals.py run log | News analyst validated classification |
| "Personnel: CTO hired, CFO departed" | News URLs, executive LinkedIn | News source + LinkedIn timestamp | HR/recruiting lead can confirm |
| "Recommendation: Hold (not Buy)" | Analyst written justification + sign-off | RUN_LOG.md + brief metadata | Analyst name + date |

**Gaps to flag:** Any claim without a row = unsupported. Do not ship the brief until each claim traces to evidence + owner.

---

## The Tell: How Do I Know I'm Using AI Appropriately Here?

✓ **I know it's right when:**
- I can re-run the recipe and get the same 6 sections (deterministic)
- I can point to the source for every signal (traceable)
- I can reject a signal and explain why to the analyst (evaluable)
- The analyst's judgment (synthesis + recommendation) is the part that changes per brief, not the data assembly (I own the irreplaceable part)

✗ **I know it's gone wrong when:**
- I'm using AI's synthesis narrative as "my reason to recommend buy" instead of a tool
- I'm approving the brief because it looks polished, not because I checked the sources
- I can't explain why Anthropic is a "hold" without re-reading the AI summary
- The recipe is making the recommendation and I'm just gate-stamping it

---

## Open Questions

1. **Signal validation (Phase 2):** Currently signals enter verified layer unvalidated. Before judgment can happen, someone human must spot-check 10+ signals for source quality. Should the recipe halt there, or does validation happen in parallel with synthesis?

2. **Competitive edge (business risk):** The brief is built on public signals only (SEC, GitHub, ArXiv, news). A competitor might have private information (direct customer conversations, exit calls, etc.). Should the brief *disclose* "this is built from public signals only" or should we have a second layer for direct research?

3. **Time pressure:** The reallocation says analyst gets 3.5 hours recovered. But if procurement needs the brief in 90 minutes, those 3 hours of judgment time disappear and we're back at step 1 (fluency trap). How do we defend the gate when business pressure is real?

4. **Materiality rule for signals:** Should a signal that is 3 years old still appear in the brief? Currently it's in the DB with `occurred_date=2023-07-09`. No age decay rule is defined. Is that a recipe gap (should be filtered) or a judgment call (analyst decides per brief)?
