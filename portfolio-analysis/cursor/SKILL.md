---
name: portfolio-analysis
description: >-
  Perform portfolio and stock analysis using Kite (Zerodha) MCP tools. Covers
  portfolio overview, single stock deep-dive, sector breakdown, mutual fund
  review, P&L attribution, and historical price analysis. Use when the user asks
  to analyze portfolio, review holdings, check positions, compare stocks, assess
  sector exposure, review mutual funds, or asks about their Zerodha/Kite
  account.
---

# Portfolio Analysis via Kite MCP

Use the **Kite MCP server** (`kite`) for all data.

> **Setup:** Ensure the Kite MCP server is configured in `.cursor/mcp.json` (workspace) or via Cursor Settings > MCP with the server name `kite`. If you used a different name, replace `kite` with your server name throughout this skill. See the [setup guide](../README.md) for details.

## Pre-flight

Before any analysis, ensure a live session:

1. Call any read tool (e.g. `get_profile`).
2. If it returns a session error, call `login` and present the auth link to the user.
3. Once authenticated, proceed with analysis.

## Analysis Types

Determine which analysis the user wants, then follow the matching workflow.

---

### 1. Full Portfolio Overview

**Trigger:** "analyze my portfolio", "portfolio summary", "how are my investments"

**Data to fetch (in parallel):**

| Tool | Purpose |
|------|---------|
| `get_profile` | Account info |
| `get_holdings` | Long-term equity holdings |
| `get_positions` | Intraday / recent positions |
| `get_mf_holdings` | Mutual fund holdings |
| `get_margins` | Cash and margin balances |

**Analysis steps:**

1. **Classify every holding** into asset class:
   - Direct Equity (stocks)
   - Gold (GOLDBEES, SGBs with `SGB` prefix)
   - Silver (SILVERBEES)
   - Fixed Income (NCDs — numeric prefix + letters, govt securities with `-GS` suffix)
   - ETFs (symbols ending in `BEES` or known ETFs)
   - Mutual Funds (from `get_mf_holdings`)

2. **Compute per-holding:**
   - Current value = `quantity × last_price`
   - Invested value = `quantity × average_price`
   - P&L = `pnl` field (or current − invested)
   - Return % = `pnl / invested × 100`
   - Day change = `quantity × day_change`
   - **Trackable Invested:** If `average_price = 0` (NCDs, govt securities allotted at par), exclude from invested total and P&L return calculation. Note this in Portfolio Summary: "Invested total excludes Fixed Income where cost basis is not recorded by the broker." This gives an honest overall return % rather than an inflated one.

3. **Aggregate into sections (in this order):**
   - Portfolio Summary table (total value, invested, P&L, cash)
   - Portfolio Health Score (see computation below)
   - Asset allocation table (class, value, % of total) — include instrument counts
   - Equity / Debt / Commodity split (high-level)
   - Today's Day Change (portfolio-wide daily P&L)
   - Direct Equity — Top 10 holdings by value
   - Best & Worst Performers (top 5 gainers, top 5 losers by return %)
   - Recovery Distance (for losers — how much gain needed to break even)
   - Return Attribution by Asset Class
   - Position Sizing Flags
   - Precious Metals breakdown — split into Gold sub-table and Silver sub-table with avg price, qty, return columns
   - Fixed Income breakdown — list each NCD and govt security individually with inferred coupon rate (from symbol prefix digits, e.g. "1140" → 11.40%), day change, and maturity notes
   - Mutual Fund performance table + MF Category breakdown
   - Today's Activity (positions opened today from `get_positions`) — include Value column
   - Sector exposure (map stocks to sectors) — include # of stocks per sector and sector-level P&L
   - All Holdings — Complete View: full table of every stock with sector tag, qty, avg, LTP, value, P&L, return %, day change %

4. **Portfolio Health Score** — Compute a 0–100 score:
   - **Diversification (25 pts):** Herfindahl Index on holding weights. HHI = Σ(weight_i²). Score = 25 × (1 − HHI/0.15). Clamp to [0, 25]. Lower HHI = better diversified.
   - **Concentration (25 pts):** Top-1 holding %. If <10%: 25 pts. 10–15%: 20 pts. 15–25%: 12 pts. >25%: 5 pts.
   - **Win Rate (25 pts):** (profitable positions / total positions) × 25.
   - **Asset Mix (25 pts):** 25 if has ≥3 asset classes with ≥10% each. 18 if ≥3 classes with ≥5% each. 12 if ≥2 classes with ≥10%. 5 otherwise.
   - Display as: `Score: XX/100 [Excellent/Good/Fair/Needs Work]`. Excellent ≥80, Good ≥65, Fair ≥50, Needs Work <50.

5. **Equity / Debt / Commodity Split:**
   - Equity = Direct Equity + Equity MFs (Flexi/Large/Mid/Small cap)
   - Debt = Fixed Income (NCDs, Govt Sec) + Debt MFs (Credit Risk funds)
   - Commodity = Gold + Silver (ETFs + SGBs)
   - Show as a 3-row table with value and %. Add a brief assessment (e.g. "equity-heavy, consider more debt for stability").

6. **Today's Day Change:**
   - Sum `quantity × day_change` across all holdings.
   - Add unrealised PnL from positions.
   - Show as: "Today your portfolio moved **+/-X,XXX** ([+/-X.X%] of total value)"

7. **Recovery Distance** (for top 5 losers by % return):
   - Breakeven gain % = `(average_price − last_price) / last_price × 100`
   - Shows how much each loser needs to rally to recover cost.

8. **Return Attribution by Asset Class:**
   - For each asset class: its P&L, and what % of total portfolio P&L it contributed.
   - Format: "Precious Metals: +62,697 → 46% of total gains"

9. **Position Sizing Flags:**
   - **Oversized** (>8% of total equity value): warn about concentration risk.
   - **Undersized** (<1% of total equity value): flag as "too small to move the needle."
   - Show as a short table.

10. **MF Category Breakdown:**
    - Classify each MF: Large Cap, Flexi Cap, Mid Cap, Small Cap, Sectoral/Thematic, Debt/Credit.
    - Infer from fund name (e.g. "LARGE CAP" → Large Cap, "FLEXI CAP" → Flexi Cap, "SMALL CAP" → Small Cap, "DEFENCE" → Sectoral/Thematic, "CREDIT RISK" → Debt).
    - Show category-level totals (invested, current, return %, % of MF portfolio).
    - Flag overlap: if user holds Large Cap MF alongside direct large-cap stocks (RELIANCE, ITC, HDFCBANK, etc.), explicitly name the overlap and suggest consolidation.

11. **Fixed Income Breakdown:**
    - List each NCD and govt security individually.
    - Infer coupon rate from trading symbol prefix (e.g. "1140IRWL27" → 11.40% coupon, "1250SFPL26" → 12.50% coupon, "699GS2026" → 6.99% govt security).
    - Show day change per instrument.
    - Note maturity year (last 2 digits of symbol, e.g. "27" → 2027).
    - Add context: "Cost basis not recorded by broker for these instruments (primary market allotment). Coupon income provides steady returns beyond market price movement."
    - Flag any instruments approaching maturity (within 1 year).

12. **All Holdings — Complete View:**
    - Full table of all direct equity stocks sorted by value (descending).
    - Columns: Stock, Sector, Qty, Avg Price, LTP, Value, P&L, Return %, Day Change %.
    - This gives a comprehensive single-table view for scanning.

13. **Key Observations & Insights** (7–10 numbered points): health score context, asset tilt, equity/debt balance, biggest creators and which asset class drove gains, weakest pockets with reasons, MF context and overlap, sector concentration, position sizing warnings, cash commentary, notable day movers.

14. **Actionable Considerations** (5–7 bullets): specific losers to review with recovery distance, profit-taking on big winners (e.g. trim after 100% run), MF overlap consolidation, debt allocation increase, undersized position resolution, MF patience/SIP context, cash/dry powder advice.

15. **Disclaimer:** End with: *"This is data-driven analysis, not financial advice. Consult a SEBI-registered advisor before making investment decisions."* followed by *"Generated on [date] from live Kite (Zerodha) data."*

**Output template:** See [templates.md](templates.md) — "Portfolio Overview".

---

### 2. Single Stock Deep-Dive

**Trigger:** "analyze RELIANCE", "tell me about INFY", "should I hold HDFC?"

**Data to fetch:**

1. `search_instruments` with `query: "<symbol>"` → get `instrument_token` and exchange.
2. `get_quotes` with `instruments: ["<exchange>:<symbol>"]` → full snapshot.
3. `get_historical_data` with the `instrument_token`:
   - 1-year daily candles: `from_date` = 1 year ago, `interval: "day"`
   - 1-month daily candles for recent trend.
4. `get_holdings` → check if user holds this stock (filter by `tradingsymbol`).

**Analysis steps:**

1. **Current snapshot:** LTP, day change %, OHLC, volume, 52-week high/low (derive from 1Y data).
2. **Technical indicators** (compute from historical candles):
   - 50-day and 200-day simple moving averages (SMA)
   - Whether price is above/below SMAs (bullish/bearish signal)
   - 52-week high/low and % away from each
   - Recent trend: up/down/sideways over last 1 month
3. **Holding context** (if held): quantity, avg price, P&L, return %, holding period.
4. **Summary opinion:** Combine technical signals into a brief outlook (bullish/bearish/neutral).

> **Important:** Always caveat that this is data-driven analysis, not financial advice.

---

### 3. Sector / Category Breakdown

**Trigger:** "sector exposure", "sector breakdown", "which sectors am I in"

**Data to fetch:** `get_holdings`, `get_positions`

**Sector mapping** — classify each stock by primary sector:

| Sector | Example Stocks |
|--------|---------------|
| Financial Services | HDFCBANK, ICICIAMC, CAMS, KFINTECH, TATACAP, FEDFINA, MASFIN |
| Metals & Mining | NATIONALUM, HINDCOPPER, HINDZINC, MIDHANI |
| Consumer/Retail | ASIANPAINT, ITC, GOCOLORS, METROBRAND, SAFARI |
| Industrial/Engineering | BLUESTARCO, KIRLOSENG, LGEINDIA, ARE&M |
| Conglomerate/Auto | RELIANCE, M&M, EXIDEIND |
| Transport | INDIGO |
| Agriculture | EIDPARRY |
| IT/Tech | INFY, TCS, WIPRO |

For stocks not in this table, infer sector from the company name or use web search.

**Output:** Table of sectors with total value, % of equity, P&L, and a concentration assessment.

---

### 4. Mutual Fund Review

**Trigger:** "MF performance", "mutual fund review", "how are my mutual funds"

**Data to fetch:** `get_mf_holdings`

**Per fund, compute:**
- Invested = `quantity × average_price`
- Current = `quantity × last_price`
- P&L = current − invested
- Return % = P&L / invested × 100

**Output:** Table with fund name, invested, current, P&L, return %. Summary of winners/losers and overall MF return.

---

### 5. Stock Comparison

**Trigger:** "compare RELIANCE and TCS", "HDFC vs ICICI"

**Data to fetch:**
1. `search_instruments` for each stock → get tokens.
2. `get_quotes` for all stocks at once.
3. `get_historical_data` for each (1Y daily).

**Compare:**
- Current price, day change %
- 1-month and 1-year return (from historical data)
- 52-week high/low proximity
- SMA signals
- If held: portfolio weight, P&L

**Output:** Side-by-side comparison table.

---

### 6. Historical Price Analysis

**Trigger:** "price history of SBIN", "how has INFY performed this year"

**Data to fetch:**
1. `search_instruments` → get `instrument_token`.
2. `get_historical_data` with appropriate date range and interval.

**Intervals guide:**
- Intraday: use `minute`, `5minute`, `15minute`, `30minute`, `60minute`
- Days/weeks: use `day`
- If user says "last month" → 30 days of `day` candles
- If user says "last year" → 365 days of `day` candles
- If user says "today" → today's `5minute` candles

**Output:** Key stats (open, high, low, close, % change over period), trend description, notable highs/lows with dates.

---

### 7. P&L Attribution

**Trigger:** "where am I making money", "biggest winners and losers", "P&L breakdown"

**Data to fetch:** `get_holdings`, `get_positions`, `get_mf_holdings`

**Analysis:**
- Rank all instruments by absolute P&L (descending)
- Show top 5 contributors and bottom 5 detractors
- Compute % of total P&L each contributes
- Separate realized vs unrealized if data available

---

## Output Guidelines

- Use markdown tables for structured data.
- Use INR formatting: commas for lakhs (e.g. 1,50,000). No currency symbol needed.
- Round percentages to 1 decimal place.
- Color-code returns in text: prefix gains with **+** and losses with **-**.
- Always end with a **Key Insights** section (3–5 bullets).
- Add disclaimer: *"This is data-driven analysis, not financial advice."*

## Kite MCP Quick Reference

Server: `kite`

| Tool | Use For | Key Args |
|------|---------|----------|
| `login` | Authenticate | none |
| `get_profile` | Account info | none |
| `get_holdings` | Long-term holdings | `from`, `limit` (optional) |
| `get_positions` | Day positions | `from`, `limit` (optional) |
| `get_mf_holdings` | Mutual funds | `from`, `limit` (optional) |
| `get_margins` | Cash & margin | none |
| `get_quotes` | Full market snapshot | `instruments: ["NSE:INFY"]` |
| `get_ltp` | Last traded price | `instruments: ["NSE:INFY"]` |
| `get_ohlc` | Day OHLC | `instruments: ["NSE:INFY"]` |
| `get_historical_data` | Historical candles | `instrument_token`, `from_date`, `to_date`, `interval` |
| `search_instruments` | Find instrument token | `query`, optional `filter_on` |
| `get_orders` | Today's orders | `from`, `limit` (optional) |
| `get_trades` | Trade history | `from`, `limit` (optional) |

For detailed tool schemas, see [reference.md](reference.md).
