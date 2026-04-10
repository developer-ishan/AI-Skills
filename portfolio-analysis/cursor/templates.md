# Output Templates

## Portfolio Overview

```markdown
## Portfolio Analysis — [User Name] ([User ID])
**Date: [Today] | Broker: [Broker]**

---

### Portfolio Summary

| Metric | Value (INR) |
|---|---|
| **Total Portfolio Value** | [X,XX,XXX] |
| Total Invested (trackable) | [X,XX,XXX] |
| **Total P&L** | [+/-XX,XXX] |
| **Overall Return** | [+/-X.X%] |
| Cash Available | [X,XXX] |

> Invested total excludes Fixed Income (NCDs/Govt Securities) where cost basis is not recorded by the broker.

---

### Portfolio Health Score: [XX]/100 — [Excellent/Good/Fair/Needs Work]

| Factor | Score | Detail |
|---|---|---|
| Diversification | [X]/25 | HHI: [X.XX] — [well/moderately/poorly] diversified |
| Concentration | [X]/25 | Top holding: [X.X%] of portfolio |
| Win Rate | [X]/25 | [N] of [M] positions profitable ([X%]) |
| Asset Mix | [X]/25 | [N] asset classes with ≥10% allocation |

[1-sentence interpretation, e.g. "Good diversification across assets, but top holding concentration is elevated."]

---

### Asset Allocation

| Asset Class | Current Value | % of Portfolio | Instruments |
|---|---|---|---|
| Direct Equity | [value] | [X.X%] | [N] stocks |
| Mutual Funds | [value] | [X.X%] | [N] funds |
| Gold (ETF + SGBs) | [value] | [X.X%] | [N] instruments |
| Silver (ETF) | [value] | [X.X%] | 1 ETF |
| Fixed Income (NCDs + GS) | [value] | [X.X%] | [N] instruments |
| Today's Positions | [value] | [X.X%] | [N] stocks |
| Cash | [value] | [X.X%] | — |

---

### Equity / Debt / Commodity Split

| Category | Value (INR) | % of Invested Assets | Components |
|---|---|---|---|
| **Equity** | [value] | [X.X%] | Direct stocks + equity MFs + today's positions |
| **Commodity** | [value] | [X.X%] | Gold ETF + SGBs + Silver ETF |
| **Debt** | [value] | [X.X%] | NCDs + Govt Securities + Credit Risk MF |

> **Assessment:** [e.g. "Portfolio is equity-heavy at 68%. Debt at 7.7% is low — consider increasing
> fixed income for better downside protection."]

---

### Today's Day Change

**Today your portfolio moved [+/-X,XXX] ([+/-X.X%] of total value)**

| Asset Class | Day Change (INR) |
|---|---|
| Direct Equity | [+/-value] |
| Silver ETF | [+/-value] |
| Gold (ETF + SGBs) | [+/-value] |
| Positions (unrealised) | [+/-value] |
| Fixed Income | [+/-value] |
| **Total** | **[+/-value]** |

> [1-sentence context, e.g. "Strong day for equities. The fixed income dip is driven by [specific NCD]."]

---

### Direct Equity — Top 10 Holdings by Value

| # | Stock | Qty | Avg Price | LTP | Current Value | P&L | Return % |
|---|---|---|---|---|---|---|---|
| 1 | [SYMBOL] | [qty] | [avg] | [ltp] | [val] | [+/-pnl] | **[+/-X.X%]** |
...

---

### Best & Worst Performers

**Top 5 Gainers (by Return %):**

| Stock | Return % | P&L (INR) | Invested | Current |
|---|---|---|---|---|
| [SYMBOL] | **[+X.X%]** | +[pnl] | [inv] | [cur] |
...

**Top 5 Losers (by Return %):**

| Stock | Return % | P&L (INR) | Invested | Current |
|---|---|---|---|---|
| [SYMBOL] | **[-X.X%]** | -[pnl] | [inv] | [cur] |
...

---

### Recovery Distance (Top 5 Losers)

| Stock | Avg Price | LTP | Loss % | Gain Needed to Break Even |
|---|---|---|---|---|
| [SYMBOL] | [avg] | [ltp] | [-X.X%] | **+[X.X%]** |
...

> [Commentary on the steepest recoveries, e.g. "SAFARI needs a 44.9% rally just to get back
> to cost — a steep climb."]

---

### Return Attribution by Asset Class

| Asset Class | P&L | % of Total Gains | Direction |
|---|---|---|---|
| Precious Metals | [+/-pnl] | [X%] | [Main driver / Contributor / Detractor] |
| Direct Equity | [+/-pnl] | [X%] | ... |
| Fixed Income | [+/-pnl] | [X%] | ... |
| Mutual Funds | [+/-pnl] | [X%] | ... |
| **Total** | **[+/-pnl]** | **100%** | |

[1-sentence: "X asset class drives Y% of your gains. Z is the main detractor."]

---

### Position Sizing Flags

**Oversized (>8% of equity):**

| Stock | Value | % of Equity | Risk |
|---|---|---|---|
| [SYMBOL] | [value] | [X.X%] | Single-stock concentration risk |
...

**Undersized (<1% of equity):**

| Stock | Value | % of Equity | Note |
|---|---|---|---|
| [SYMBOL] | [value] | [X.X%] | Too small to meaningfully impact returns |
...

[1-sentence: "Consider trimming oversized positions and consolidating or exiting tiny ones."]

---

### Precious Metals Breakdown

**Gold Holdings:**

| Instrument | Qty | Avg Price | LTP | Current Value | P&L | Return % |
|---|---|---|---|---|---|---|
| GOLDBEES (ETF) | [qty] | [avg] | [ltp] | [value] | [+/-pnl] | [+/-X.X%] |
| [SGB name] | [qty] | [avg] | [ltp] | [value] | [+/-pnl] | [+/-X.X%] |
...
| **Total Gold** | | | | **[value]** | **[+/-pnl]** | **[+/-X.X%]** |

**Silver Holdings:**

| Instrument | Qty | Avg Price | LTP | Current Value | P&L | Return % |
|---|---|---|---|---|---|---|
| SILVERBEES (ETF) | [qty] | [avg] | [ltp] | [value] | [+/-pnl] | **[+/-X.X%]** |

> **Total Precious Metals: [value] ([X.X%] of portfolio)** — [commentary on performance and contribution.]

---

### Fixed Income Holdings

| Instrument | Type | Qty | LTP | Current Value | Day Change |
|---|---|---|---|---|---|
| [Symbol] | NCD ([X.XX%]) | [qty] | [ltp] | [value] | [+/-change] |
| [Symbol] | Govt Security | [qty] | [ltp] | [value] | [+/-change] |
...
| **Total FI** | | | | **[value]** | **[+/-change]** |

> Cost basis not recorded by broker for these instruments (primary market allotment).
> Coupon income provides steady returns beyond market price movement.
> [Flag any NCDs approaching maturity within 1 year.]

---

### Mutual Funds — All [Underwater/Mixed/Positive]

| Fund | Category | Invested | Current | P&L | Return % |
|---|---|---|---|---|---|
| [Fund Name] – Direct | [Large Cap] | [inv] | [cur] | [+/-pnl] | **[+/-X.X%]** |
...
| **Total MF** | | **[inv]** | **[cur]** | **[+/-pnl]** | **[+/-X.X%]** |

**MF Category Breakdown:**

| Category | Invested | Current | Return % | % of MF Portfolio |
|---|---|---|---|---|
| Flexi Cap ([N] funds) | [inv] | [cur] | [+/-X.X%] | [X.X%] |
| Large Cap ([N] fund) | [inv] | [cur] | [+/-X.X%] | [X.X%] |
| Large & Mid Cap ([N] fund) | [inv] | [cur] | [+/-X.X%] | [X.X%] |
| Sectoral/Thematic ([N] fund) | [inv] | [cur] | [+/-X.X%] | [X.X%] |
| Small Cap ([N] fund) | [inv] | [cur] | [+/-X.X%] | [X.X%] |
| Debt/Credit ([N] fund) | [inv] | [cur] | [+/-X.X%] | [X.X%] |

> **Overlap Alert:** [If applicable: "You hold Large Cap MF + Large & Mid Cap MF alongside
> direct large-cap stocks like RELIANCE, ITC, M&M. Consider whether you need both MFs plus
> direct large-cap exposure."]

---

### Today's Activity (Positions)

| Stock | Action | Qty | Avg Price | LTP | Value | Unrealised P&L |
|---|---|---|---|---|---|---|
| [SYMBOL] | Buy | [qty] | [price] | [ltp] | [value] | [+/-pnl] |
...
| **Total** | | | | | **[value]** | **[+/-pnl]** |

> [1-sentence: e.g. "Both financial sector buys are in green. These will convert to holdings upon delivery settlement."]

---

### Sector Exposure (Direct Equity + Positions)

| Sector | Value (INR) | % of Equity | # Stocks | Sector P&L |
|---|---|---|---|---|
| [Sector] | [value] | [X.X%] | [N] | [+/-pnl] |
...

> **Concentration Assessment:** [e.g. "Financial Services is the largest sector at 28.6%. Metals
> & Mining has been the strongest value creator. Consumer/Retail and Agriculture are weakest."]

---

### All Holdings — Complete View

| Stock | Sector | Qty | Avg Price | LTP | Value | P&L | Return % | Day Chg % |
|---|---|---|---|---|---|---|---|---|
| [SYMBOL] | [Sector] | [qty] | [avg] | [ltp] | [val] | [+/-pnl] | [+/-X.X%] | [+/-X.X%] |
...

(Sorted by value descending. Provides full portfolio transparency in a single scannable table.)

---

### Key Observations & Insights

Provide 7–10 numbered insights covering:
1. Portfolio health score interpretation — what's strong, what drags the score
2. Precious metals as a return driver — % of total gains they contributed
3. Equity win rate — how many stocks are green vs red, and the spread
4. MF as the weakest link — all equity MFs negative, possible causes
5. Equity-heavy tilt — debt allocation too low for stability
6. Sector concentration — largest sector bet and sub-sector diversity
7. Star sector — which sector contributed the most P&L
8. Struggling sector — which sector/theme is in the red
9. Position sizing — any borderline oversized or undersized holdings
10. Today's performance — broad market context

---

### Actionable Considerations

Provide 5–7 bullet-point action items:
- **Review deep losers:** [stocks] need [X–Y%] rallies to break even — evaluate if thesis holds or book losses for tax harvesting.
- **Trim big winners:** [stock] is up [X%] and oversized at [Y%] of equity — consider partial profit booking.
- **Address MF underperformance:** If SIP, continue with patience. If lump-sum, consider averaging down.
- **Increase debt allocation:** At [X%], portfolio is under-allocated. Target [15–20%] for stability.
- **Resolve tiny positions:** [stock] at [X%] of equity can't impact returns — add more or exit.
- **Reduce MF overlap:** [Specific overlap] — consider keeping one fund and relying on direct picks.
- **Build cash buffer:** At [X] in cash, maintain liquidity for buying dips. Target [5–10%] buffer.

*This is data-driven analysis, not financial advice. Consult a SEBI-registered advisor before making investment decisions.*

*Generated on [date] from live Kite (Zerodha) data.*
```

---

## Single Stock Deep-Dive

```markdown
## Stock Analysis: [SYMBOL] ([Exchange])

**Date: [Today]**

### Current Snapshot

| Metric | Value |
|---|---|
| Last Traded Price | [LTP] |
| Day Change | [+/-X.XX] ([+/-X.X%]) |
| Open / High / Low / Close | [O] / [H] / [L] / [C] |
| Volume | [vol] |
| 52-Week High | [high] ([date]) — [X.X%] away |
| 52-Week Low | [low] ([date]) — [+X.X%] above |

### Technical Signals

| Indicator | Value | Signal |
|---|---|---|
| 50-Day SMA | [value] | Price [above/below] → [Bullish/Bearish] |
| 200-Day SMA | [value] | Price [above/below] → [Bullish/Bearish] |
| 1-Month Trend | [+/-X.X%] | [Uptrend/Downtrend/Sideways] |
| 1-Year Return | [+/-X.X%] | [context] |

### Your Holding (if applicable)

| Metric | Value |
|---|---|
| Quantity | [qty] |
| Average Price | [avg] |
| Current Value | [value] |
| P&L | [+/-pnl] ([+/-X.X%]) |

### Summary

[2-3 sentence outlook combining technical signals and context]

*This is data-driven analysis, not financial advice.*
```

---

## Stock Comparison

```markdown
## Stock Comparison: [SYMBOL1] vs [SYMBOL2]

| Metric | [SYMBOL1] | [SYMBOL2] |
|---|---|---|
| LTP | [ltp1] | [ltp2] |
| Day Change | [+/-X.X%] | [+/-X.X%] |
| 1-Month Return | [+/-X.X%] | [+/-X.X%] |
| 1-Year Return | [+/-X.X%] | [+/-X.X%] |
| 52W High (% away) | [X.X%] | [X.X%] |
| 52W Low (% above) | [X.X%] | [X.X%] |
| 50-Day SMA Signal | [Bull/Bear] | [Bull/Bear] |
| 200-Day SMA Signal | [Bull/Bear] | [Bull/Bear] |
| Held in Portfolio? | [Yes/No] | [Yes/No] |
| Portfolio P&L | [+/-X.X%] | [+/-X.X%] |

### Verdict

[Brief comparison summary]

*This is data-driven analysis, not financial advice.*
```
