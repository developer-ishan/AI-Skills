# Kite MCP Tool Reference

Server identifier: `kite`

---

## Authentication

### login
Initiates Kite API login. Returns a URL the user must click to authorize.
Call this if any tool returns a session/auth error.

```
CallMcpTool: server=kite, toolName=login, arguments={}
```

---

## Account & Portfolio Tools

### get_profile
Returns: `user_id`, `user_name`, `email`, `broker`, `products`, `order_types`, `exchanges`.

### get_holdings
Returns array of holdings. Each holding:
```
tradingsymbol, exchange, instrument_token, isin, product,
quantity, average_price, last_price, close_price,
pnl, day_change, day_change_percentage,
t1_quantity, collateral_quantity, discrepancy
```

- `average_price = 0` typically means allotted/gifted instruments (IPO, bonds, SGBs at subscription).
- `pnl` = `(last_price - average_price) × quantity`.
- Pagination: pass `from` (0-based) and `limit` for large portfolios.

### get_positions
Returns array of open positions (intraday + delivery bought today).
Each position:
```
tradingsymbol, exchange, product, quantity, average_price,
last_price, pnl, m2m, unrealised, realised,
buy_quantity, buy_price, buy_value,
sell_quantity, sell_price, sell_value
```

### get_mf_holdings
Returns array of mutual fund holdings:
```
folio, fund, tradingsymbol, average_price, last_price, quantity, pnl
```

- Invested = `quantity × average_price`
- Current = `quantity × last_price`
- Note: `pnl` field may be 0; compute manually from prices.

### get_margins
Returns equity and commodity margin data:
```
equity: { net, available: { cash, collateral, intraday_payin, live_balance, opening_balance }, utilised: { debits, ... } }
commodity: { ... }
```

---

## Market Data Tools

### get_quotes
Full market snapshot for up to 500 instruments.
```
arguments: { instruments: ["NSE:INFY", "BSE:RELIANCE"] }
```
Returns per instrument: LTP, OHLC, volume, buy/sell depth, last_trade_time, oi, etc.

### get_ltp
Lightweight — only last traded price.
```
arguments: { instruments: ["NSE:INFY"] }
```

### get_ohlc
Day's Open, High, Low, Close plus close of previous day.
```
arguments: { instruments: ["NSE:INFY"] }
```

### get_historical_data
Historical OHLC candles. **Requires `instrument_token`** (get from `search_instruments`).
```
arguments: {
  instrument_token: 408065,
  from_date: "2025-04-10 00:00:00",
  to_date: "2026-04-10 00:00:00",
  interval: "day"
}
```

Intervals: `minute`, `3minute`, `5minute`, `10minute`, `15minute`, `30minute`, `60minute`, `day`.

Returns array of candles: `[timestamp, open, high, low, close, volume]`.

### search_instruments
Find instruments by name, symbol, or ISIN.
```
arguments: { query: "RELIANCE", filter_on: "name", limit: 5 }
```

`filter_on` options:
- `id` (default) — `exchange:tradingsymbol` format
- `name` — company nice name
- `tradingsymbol` — exchange-specific symbol
- `isin` — universal ISIN code
- `underlying` — find F&O for an underlying

Returns: `tradingsymbol`, `exchange`, `instrument_token`, `name`, `instrument_type`, `lot_size`, etc.

---

## Order & Trade Tools

### get_orders
All orders for current day. Fields: `order_id`, `tradingsymbol`, `transaction_type`, `quantity`, `price`, `status`, etc.

### get_trades
Executed trades for current day. Fields: `trade_id`, `order_id`, `tradingsymbol`, `quantity`, `average_price`, etc.

### get_order_history
History of a specific order. Arguments: `order_id` (required).

### get_order_trades
Trades for a specific order. Arguments: `order_id` (required).

---

## Instrument Token Quick Reference

Common instrument tokens for frequently queried stocks (use `search_instruments` for others):

| Stock | Exchange | Token |
|-------|----------|-------|
| RELIANCE | NSE | 738561 |
| ITC | NSE | 424961 |
| ASIANPAINT | NSE | 60417 |
| EXIDEIND | NSE | 173057 |
| GOLDBEES | NSE | 3693569 |
| SILVERBEES | NSE | 2068481 |
| INDIGO | NSE | 2865921 |

---

## Portfolio Health Computations

### Herfindahl-Hirschman Index (HHI)
Measures concentration. Computed on all holdings (equity + ETF + SGB + positions):
```
weight_i = holding_value_i / total_portfolio_value
HHI = Σ(weight_i²)
```
- HHI < 0.05: Well diversified
- 0.05–0.10: Moderately diversified
- 0.10–0.15: Concentrated
- > 0.15: Highly concentrated

### Recovery Distance
For any losing position:
```
Recovery % = (average_price − last_price) / last_price × 100
```
This is always larger than the loss %. A -50% loss requires a +100% gain to recover.

### Return Attribution
For each asset class:
```
Contribution % = asset_class_pnl / total_portfolio_pnl × 100
```
If total P&L is positive, positive contributors show what drove gains.
If total P&L is negative, negative contributors show what drove losses.

### MF Category Inference
Classify from fund name keywords:
- "LARGE CAP" → Large Cap
- "FLEXI CAP" → Flexi Cap
- "SMALL CAP" → Small Cap
- "MID CAP" or "LARGE & MID" → Large & Mid Cap
- "DEFENCE", "PHARMA", "IT", "INFRA" → Sectoral/Thematic
- "CREDIT RISK", "LIQUID", "SHORT TERM", "GILT" → Debt
- "INDEX" (with equity focus) → Index/Passive

### Position Sizing Thresholds
Compute weight relative to total equity value (stocks + positions only, not MFs/ETFs/bonds):
- **Oversized:** weight > 8% → concentration risk warning
- **Undersized:** weight < 1% → "too small to matter" flag

### Trackable Invested Total
Holdings with `average_price = 0` (NCDs, govt securities from primary allotment) have no recorded cost basis. Exclude them from:
- Total Invested calculation
- Overall Return % calculation
- Return Attribution percentages

Report as: "Invested total excludes Fixed Income where cost basis is not recorded by the broker." This avoids inflating the return (e.g. reporting +16% when honest return on trackable capital is +8%).

### NCD Coupon Rate Inference
Extract coupon rate from NCD trading symbols. The leading digits (before the company code) typically encode the coupon rate with an implied decimal:
- `1140IRWL27` → **11.40%** coupon, IRWL issuer, maturing 2027
- `1250SFPL26` → **12.50%** coupon, SFPL issuer, maturing 2026
- `1150AFIL28` → **11.50%** coupon, AFIL issuer, maturing 2028
- `125BCSL29` → **12.5%** coupon, BCSL issuer, maturing 2029
- `11DKGFSF28` → **11%** coupon, DKGFSF issuer, maturing 2028

Pattern: first 3-4 digits ÷ 100 = coupon %. Last 2 digits = maturity year.

For govt securities: `699GS2026` → **6.99%** coupon, maturing 2026.

Flag instruments maturing within 1 year of analysis date (expect face value redemption).

---

## Computing Technical Indicators

### Simple Moving Average (SMA)
From daily candles array, compute:
```
SMA_N = sum(close prices of last N candles) / N
```
- SMA 50: use last 50 daily closes
- SMA 200: use last 200 daily closes
- Bullish if price > SMA 50 > SMA 200 (golden cross territory)
- Bearish if price < SMA 50 < SMA 200 (death cross territory)

### 52-Week High/Low
From 252 daily candles (1 trading year):
- 52W High = max(high) across all candles
- 52W Low = min(low) across all candles
- % from high = (LTP - 52W High) / 52W High × 100
- % from low = (LTP - 52W Low) / 52W Low × 100

### Trend Assessment
Compare first and last candle in period:
- Return % = (last close - first close) / first close × 100
- If > +5%: "Uptrend"
- If < -5%: "Downtrend"
- Otherwise: "Sideways/Consolidating"

---

## Sector Classification

Use this mapping for Indian stocks. For unlisted stocks, infer from name or search web.

| Sector | Stocks |
|--------|--------|
| Banking | HDFCBANK, ICICIBANK, SBIN, KOTAKBANK, AXISBANK, BANDHANBNK |
| Financial Services | ICICIAMC, HDFCAMC, CAMS, KFINTECH, TATACAP, FEDFINA, MASFIN, BAJFINANCE |
| IT | INFY, TCS, WIPRO, HCLTECH, TECHM, LTIM |
| Consumer Staples | ITC, HINDUNILVR, NESTLEIND, BRITANNIA, DABUR, MARICO |
| Consumer Discretionary | ASIANPAINT, TITAN, GOCOLORS, METROBRAND, SAFARI, TRENT |
| Metals & Mining | NATIONALUM, HINDCOPPER, HINDZINC, TATASTEEL, JSWSTEEL, VEDL, COALINDIA |
| Industrial/Engineering | BLUESTARCO, KIRLOSENG, LGEINDIA, ARE&M, SIEMENS, ABB, BEL |
| Defence | MIDHANI, HAL, BEL, BDL, MAZAGON |
| Oil & Gas | RELIANCE, ONGC, IOC, BPCL, GAIL |
| Auto | M&M, TATAMOTORS, MARUTI, BAJAJ-AUTO, EXIDEIND, HEROMOTOCO |
| Pharma | SUNPHARMA, DRREDDY, CIPLA, DIVISLAB, BIOCON |
| Telecom | BHARTIARTL, IDEA |
| Transport | INDIGO, IRCTC |
| Agriculture/Sugar | EIDPARRY, BALRAMCHIN, RENUKA |
| Real Estate | DLF, GODREJPROP, OBEROIRLTY, PRESTIGE |
