# Quaz Scalping — Final AI Analysis, Buy/Sell & GTT Specification

## 1. Objective

Build Quaz Scalping as a disciplined, AI-assisted short-term stock trading system using:

- Zerodha Kite Connect/API for broker integration and GTT/order execution
- Claude API for market, fundamental, technical, entry and exit analysis
- A deterministic Quaz Risk Engine for all hard safety rules
- Flutter Android for the mobile control panel
- .NET backend for continuous processing, Claude integration, market analysis, risk validation and broker execution

**Core principle:**

> Claude analyzes. Quaz Risk Engine decides whether trading is permitted. GTT Engine executes through Zerodha.

Claude must never directly place, modify or cancel orders/GTTs.

---

# 2. Trading Scope

Quaz is strictly for short-term stock trading using approved equities.

## Prohibited

- Futures
- Options
- F&O
- Leverage
- Short selling
- Unapproved stocks
- Automatic discovery-to-trade without user approval
- Automated averaging down
- Automated loss-making exits

The app must not promise guaranteed profit.

For clarity, the application should describe this mode as **short-term/GTT-based trading**, rather than claiming that it can guarantee "scalping" profits.

---

# 3. Recommended Architecture

```text
                    ┌──────────────────────┐
                    │    Flutter Android   │
                    │  Dashboard / Config  │
                    └──────────┬───────────┘
                               │ HTTPS
                               ▼
                    ┌──────────────────────┐
                    │   Quaz .NET Backend  │
                    │                      │
                    │ Market Data           │
                    │ Technical Engine      │
                    │ Fundamental Engine    │
                    │ Schedule Engine       │
                    │ Claude AI Gateway     │
                    │ Risk Engine           │
                    │ GTT Engine            │
                    │ Position Manager      │
                    └───────┬─────────┬────┘
                            │         │
                 ┌──────────┘         └──────────┐
                 ▼                               ▼
        ┌─────────────────┐             ┌─────────────────┐
        │  Claude API     │             │ Zerodha Kite API│
        │  Analysis only  │             │   Execution     │
        └─────────────────┘             └─────────────────┘
```

The backend must continue operating when the phone is locked or disconnected.

Android should provide:

- status monitoring
- STOP SCALPING
- notifications
- configuration
- biometric authentication
- trade history
- portfolio information

---

# 4. Claude API Configuration

Do not expose the Anthropic API key in the Android APK.

Preferred:

```text
Flutter Android
      ↓
Quaz Backend
      ↓
Anthropic Claude API
```

Backend secret:

```text
ANTHROPIC_API_KEY=********
```

Never:

- hard-code API keys
- store keys in Git
- expose keys to the Android app
- expose keys to Claude
- expose keys in logs
- expose keys in notifications

The Android app may contain only the authenticated connection to the Quaz backend.

---

# 5. Claude Configuration Screen

Create an AI Configuration section with:

```text
AI Provider:
Claude

Model:
[ configurable ]

API Key:
••••••••••••••••

Status:
Connected / Invalid / Not Configured

[ TEST CONNECTION ]

[ REPLACE API KEY ]
```

Do not reveal the stored key after saving.

Allow configurable prompts:

```text
Stock Suggestion Prompt
Fundamental Analysis Prompt
Market Analysis Prompt
Chart Analysis Prompt
Pre-Buy Prompt
Buy GTT Validation Prompt
Sell Analysis Prompt
Sell GTT Validation Prompt
Psychology Prompt
```

Each prompt must support:

- Default prompt
- User custom prompt
- Reset to default
- Prompt version
- Enabled/disabled state

Protected settings require biometric authentication.

---

# 6. AI Responsibility

Claude may:

- analyze companies
- analyze sectors
- analyze market conditions
- rank user-approved stocks
- suggest additional stocks for user review
- analyze charts
- identify entry opportunities
- identify profitable exit opportunities
- explain risk
- detect FOMO
- explain why a trade should wait
- explain rejected trades

Claude must NOT:

- place orders
- create GTTs
- modify GTTs
- cancel GTTs
- change capital limits
- change whitelist
- change blocklist
- disable profit-only protection
- override RiskEngine
- access broker credentials
- guarantee returns

If data is stale, missing, contradictory or unreliable:

```text
NO_TRADE
```

---

# 7. Three-Axis Fundamental Framework

## X Axis — Quality / Fundamental

Analyze:

- business quality
- competitive advantage
- moat
- market share
- brand
- pricing power
- recurring revenue
- customer stickiness
- business model
- management
- capital allocation
- industry position
- global competitiveness

## Y Axis — Growth

Analyze yearly and multi-year trends:

- revenue
- EBITDA
- EBIT
- PAT
- EPS
- operating cash flow
- free cash flow
- margins
- ROE
- ROCE
- ROIC
- assets
- capacity
- market share
- order book
- order execution

Also examine:

- debt change
- receivables
- inventory
- working capital
- cash conversion

High growth is valuable only when it is high-quality and sustainable.

## Z Axis — Valuation

Analyze:

- P/E
- forward P/E where reliable
- P/B
- EV/EBITDA
- EV/Sales where appropriate
- FCF yield
- PEG where appropriate
- dividend yield where relevant
- historical valuation
- peer valuation
- growth-adjusted valuation

Never classify a stock as cheap only because its P/E is low.

---

# 8. S-C-R-A-P Framework

## S — Sector

Analyze:

- sector growth
- industry CAGR
- future demand
- sector cycle
- government/regulatory support
- competitive intensity
- technology disruption
- capacity expansion
- global demand
- commodity exposure
- import/export exposure
- global supply chain

A future sector growth rate above 15% is a strong positive factor, not an unconditional requirement.

## C — Compounding Engines

Analyze:

- revenue growth
- earnings growth
- margin expansion
- pricing power
- capacity expansion
- market-share gains
- recurring revenue
- operating leverage
- asset turnover
- ROIC improvement
- new products
- new markets
- acquisitions
- order-book growth
- order execution

Growth above 25% YoY is a strong positive factor, not an automatic requirement.

## R — Red Flags / Rejection Criteria

### Debt

- debt/equity
- net debt
- interest coverage
- debt maturity
- refinancing risk

### Cash Flow

- operating cash flow
- free cash flow
- PAT vs operating cash flow
- cash conversion

### Working Capital

- receivables
- inventory
- payable days
- working capital intensity

### Promoters

- promoter holding
- annual promoter holding changes
- promoter pledge
- promoter buying
- promoter selling
- dilution
- warrants
- preferential allotments

### Governance

- related-party transactions
- auditor changes
- auditor remuneration
- qualified audit opinion
- contingent liabilities
- regulatory action
- unusual subsidiaries
- unexplained loans/advances
- governance issues

Severe red flags must produce:

```text
REJECT
```

## A — Allocation

For long-term portfolio analysis:

- approximately 15–20 stocks as an ideal diversified portfolio
- sector diversification
- concentration risk
- position sizing
- conviction
- valuation
- correlation

Do not mix long-term allocation with the short-term automated trading universe.

## P — Psychology

Detect:

- FOMO
- revenge trading
- chasing breakouts
- averaging down
- overtrading
- panic selling
- emotional target changes
- buying after an extended move

Default principle:

> No trade is better than a bad trade.

---

# 9. Investor-Style Analytical Lenses

Use these as analytical frameworks, not as claims that the named investor would buy a specific stock.

## Buffett-style

- quality
- durability
- moat
- management
- valuation
- capital allocation

## Peter Lynch-style

- growth
- valuation
- understandable business
- earnings potential

## Growth + Quality

- sustainable growth
- ROIC/ROCE
- margins
- balance sheet

## Growth + Valuation

- earnings growth
- reasonable valuation
- growth-adjusted valuation

---

# 10. Fundamental Score

Default weighting:

```text
Quality              25%
Growth               25%
Valuation            20%
Balance Sheet        10%
Cash Flow             10%
Management             5%
Governance             5%
```

Return:

```text
fundamental_score
quality_score
growth_score
valuation_score
balance_sheet_score
cash_flow_score
management_score
governance_score
sector_score
```

Each score is 0–100.

Do not trigger a trade from fundamental score alone.

---

# 11. Global Supply Chain Analysis

Evaluate:

- raw material dependency
- supplier concentration
- import dependency
- export dependency
- country concentration
- geopolitical exposure
- shipping/logistics exposure
- commodity price sensitivity
- currency exposure
- semiconductor dependency where applicable
- critical component dependency
- supply-chain resilience
- alternate suppliers
- manufacturing capacity

Explain how supply-chain changes can affect revenue, margin and future growth.

---

# 12. Management Track Record

Analyze:

- historical execution
- capital allocation
- acquisitions
- debt decisions
- buybacks
- dividends
- promoter transactions
- related-party activity
- communication consistency
- historical guidance vs actual results
- treatment of minority shareholders
- governance history

---

# 13. Future Order Book

Analyze:

- order-book size
- order-book growth
- order-book/revenue ratio
- customer quality
- order concentration
- cancellation risk
- execution timeline
- margin potential
- repeat orders
- capacity required
- working-capital requirement

Do not treat a large order book as automatically positive.

---

# 14. Stock Suggestion Prompt

## Purpose

Suggest stocks that deserve consideration for the user's research/watchlist.

The AI must NOT automatically add them to the whitelist.

### Default Prompt

```text
You are the Fundamental and Market Research Analyst for Quaz.

Identify potentially attractive stocks for user review.

Use the S-C-R-A-P framework and the X/Y/Z framework.

Analyze:

1. Sector
2. Business quality
3. Competitive advantage
4. Growth
5. Revenue
6. EBITDA
7. EPS
8. PAT
9. Operating cash flow
10. Free cash flow
11. ROE
12. ROCE
13. ROIC
14. Margins
15. Debt
16. Working capital
17. Receivables
18. Inventory
19. Cash conversion
20. Promoter holding
21. Promoter pledge
22. Promoter transactions
23. Dilution
24. Management track record
25. Capital allocation
26. Future order book
27. Order execution
28. Global supply chain
29. Customer concentration
30. Related-party transactions
31. Auditor issues
32. Governance
33. Valuation
34. Historical valuation
35. Peer valuation
36. Major risks

Return:

- fundamental score
- quality score
- growth score
- valuation score
- balance-sheet score
- cash-flow score
- management score
- governance score
- sector score
- compounding engines
- red flags
- major risks
- reason

Classification:

STRONG_CANDIDATE
CANDIDATE
WATCH
AVOID
REJECT

Do not automatically add the stock to the user's whitelist.

Do not provide guaranteed future returns.
Do not invent missing data.
```

---

# 15. User Approval for Suggested Stocks

Suggested stock:

```text
AMD

Fundamental Score: 88
Growth: 94
Quality: 90
Valuation: 68

Reason:
...

[ ADD TO WHITELIST ]
[ IGNORE ]
```

Only the user can add it.

AI cannot directly modify the whitelist.

---

# 16. Market-Regime Analysis

Before automated BUY analysis, evaluate:

- NIFTY/SENSEX
- NASDAQ where applicable
- S&P 500 where applicable
- market breadth
- volatility
- sector trend
- crude oil
- bond yields
- currency
- global market conditions
- relevant macro/news risk

Classify:

```text
STRONG_BULLISH
BULLISH
NEUTRAL
BEARISH
STRONG_BEARISH
```

If market conditions are highly uncertain:

```text
NO_TRADE
```

---

# 17. Technical / Chart Analysis

The backend Technical Engine must calculate indicators.

Claude must interpret supplied values and must not invent them.

Analyze:

## Timeframes

- 1m
- 3m
- 5m
- 15m
- 30m when useful

## Indicators

- OHLC
- volume
- VWAP
- EMA 9
- EMA 20
- EMA 50
- RSI
- MACD
- ATR
- relative volume
- volatility
- spread
- liquidity

## Price Structure

- higher highs
- higher lows
- lower highs
- lower lows
- support
- resistance
- previous high
- previous low
- breakout
- breakout retest
- false breakout
- gap
- candle confirmation
- momentum

Do not trade from one indicator.

Require multiple independent confirmations.

---

# 18. Chart Analysis Prompt

```text
Analyze the supplied OHLCV and technical indicator data.

Do not invent prices, indicators or market conditions.

Analyze:

- market structure
- trend
- higher highs/lows
- lower highs/lows
- VWAP
- EMA 9/20/50
- RSI
- MACD
- ATR
- relative volume
- support
- resistance
- breakout
- retest
- false breakout risk
- candle confirmation
- momentum
- volatility
- liquidity
- spread

Compare:

1m
3m
5m
15m
30m where available.

Look for multi-timeframe confirmation.

Return:

STRONG_BULLISH
BULLISH
NEUTRAL
BEARISH
STRONG_BEARISH

and:

ENTRY_NOW
WAIT
AVOID

Provide:

- strongest confirming signals
- conflicting signals
- preferred entry zone
- target zone
- invalidation conditions
- risk/reward
- major risks

If data is stale, incomplete or contradictory:

NO_TRADE.
```

---

# 19. Technical Score

Default:

```text
Market Regime       15%
Trend               15%
VWAP                15%
Volume              15%
Momentum            10%
EMA Alignment       10%
Support/Resistance  10%
Liquidity             5%
Volatility            5%
```

Default minimum:

```text
75 / 100
```

Make the threshold configurable.

---

# 20. Pre-BUY Analysis

Before any BUY GTT, answer:

> Is NOW a sufficiently good entry based on current market, sector and technical evidence?

Evaluate:

### Fundamental

- quality
- growth
- valuation
- balance sheet
- cash flow
- management
- governance

### Market

- broad market
- sector
- volatility
- macro environment

### Technical

- price action
- VWAP
- EMA
- RSI
- MACD
- ATR
- volume
- relative volume
- support
- resistance
- breakout/retest
- momentum
- liquidity
- spread

Determine:

- entry zone
- preferred entry
- target
- risk/reward
- expected holding period
- confidence
- invalidation conditions

Possible decisions:

```text
BUY_CANDIDATE
WAIT_FOR_ENTRY
NO_TRADE
REJECT
```

A BUY_CANDIDATE requires:

```text
User-approved stock
+
Acceptable market
+
Acceptable sector
+
Strong technical setup
+
Acceptable risk/reward
+
Expected net profit after charges
+
All RiskEngine checks pass
```

A strong company with a poor current entry must produce:

```text
WAIT
```

---

# 21. Pre-BUY JSON

```json
{
  "symbol": "SYMBOL",
  "decision": "BUY_CANDIDATE",
  "fundamental_score": 0,
  "technical_score": 0,
  "market_score": 0,
  "sector_score": 0,
  "entry_score": 0,
  "confidence": 0,
  "market_regime": "BULLISH",
  "trend": "BULLISH",
  "momentum": "STRONG",
  "volume_confirmation": true,
  "vwap_status": "ABOVE",
  "ema_alignment": "BULLISH",
  "support": 0,
  "resistance": 0,
  "entry_zone": {
    "low": 0,
    "high": 0
  },
  "preferred_entry": 0,
  "target_zone": {
    "low": 0,
    "high": 0
  },
  "risk_reward": 0,
  "expected_holding_minutes": 0,
  "expected_net_profit": 0,
  "reason": "",
  "risks": [],
  "invalidation_conditions": []
}
```

---

# 22. Final BUY Risk Validation

After Claude returns BUY_CANDIDATE, RiskEngine independently checks:

1. Stock is in whitelist
2. Stock is not in pre-existing holdings
3. Stock is not already a Quaz position
4. No duplicate pending order
5. No duplicate GTT
6. Market is open
7. Trading day is valid
8. Today is not an exchange holiday
9. Time is inside allowed entry window
10. STOP SCALPING is OFF
11. Automation is active
12. Market data is fresh
13. Zerodha connection is healthy
14. Broker session is valid
15. Capital is available
16. Per-trade limit passes
17. Per-stock limit passes
18. Total capital limit passes
19. Daily trade limit passes
20. Concurrent-position limit passes
21. Quantity is valid
22. Instrument is valid
23. Expected net profit exceeds charges
24. Technical score meets minimum
25. Risk/reward meets minimum
26. User blocklist passes
27. System blocklist passes
28. Latest price remains within validated entry conditions

If ANY check fails:

```text
REJECT
```

The rejection reason must be recorded.

Claude cannot override a failed check.

---

# 23. BUY GTT Construction

Claude provides:

- entry analysis
- preferred entry
- target
- confidence
- reasoning

The application calculates:

- quantity
- capital
- trigger
- limit price
- charges
- expected net profit

The GTT Engine performs final validation.

Only GTT Engine can send the request to Zerodha.

---

# 24. Profit Calculation

Never define profit as:

```text
LTP > Entry Price
```

Use:

```text
Gross Exit Value
-
Entry Cost
-
Brokerage
-
Exchange Charges
-
Taxes
-
Applicable Transaction Costs
=
Estimated Net P&L
```

Use the correct current charge calculation for the relevant Zerodha product/exchange.

---

# 25. Profit-Only SELL

This is a hard deterministic rule.

Calculate:

```text
minimum_profitable_exit_price
```

which covers:

- entry cost
- exit charges
- applicable taxes/fees
- configured minimum profit

If:

```text
estimated_net_profit <= 0
```

then:

```text
DO_NOT_SELL
```

If user minimum profit is configured and:

```text
estimated_net_profit < minimum_required_profit
```

then:

```text
DO_NOT_SELL
```

Claude cannot override this.

---

# 26. SELL Analysis Prompt

```text
You are the SELL ANALYSIS ENGINE for Quaz.

Analyze an existing Quaz-generated position.

Objective:

Identify a reasonable opportunity to exit with NET PROFIT.

Input:

- symbol
- quantity
- average entry price
- current price
- VWAP
- EMA 9/20/50
- RSI
- MACD
- ATR
- volume
- support
- resistance
- market trend
- sector trend
- volatility
- liquidity
- estimated charges
- minimum profitable exit
- required minimum profit

Analyze:

- current trend
- momentum
- VWAP
- EMA alignment
- RSI
- MACD
- volume
- support
- resistance
- market trend
- sector trend
- target probability
- reversal risk

Possible decisions:

SELL_PROFIT_CANDIDATE
HOLD_FOR_HIGHER_PROFIT
HOLD
WAIT
DO_NOT_SELL

Hard rule:

If the proposed exit would result in net loss:

DO_NOT_SELL

Never override the profit-only rule.

Do not guarantee profit.
Do not invent missing data.
```

---

# 27. SELL JSON

```json
{
  "symbol": "SYMBOL",
  "decision": "SELL_PROFIT_CANDIDATE",
  "current_price": 0,
  "entry_price": 0,
  "minimum_profitable_exit": 0,
  "recommended_sell_price": 0,
  "estimated_net_profit": 0,
  "profit_percent": 0,
  "technical_trend": "BULLISH",
  "momentum": "STRONG",
  "resistance": 0,
  "confidence": 0,
  "reason": "",
  "risks": []
}
```

---

# 28. SELL GTT Validation

Only create a SELL GTT when:

```text
Recommended Sell Price
>
Minimum Profitable Exit
```

AND:

- position is a Quaz-generated position
- position is still open
- quantity is correct
- no conflicting order exists
- current market data is valid
- GTT parameters are valid
- profit-only check passes

Otherwise:

```text
DO_NOT_SELL
```

---

# 29. Existing Holdings Protection

Before every automated BUY:

Synchronize:

```text
Zerodha Holdings
+
Zerodha Positions
+
Pending Orders
```

Effective automated universe:

```text
USER WHITELIST
-
PRE-EXISTING HOLDINGS
-
PRE-EXISTING POSITIONS
-
USER BLOCKLIST
-
SYSTEM BLOCKLIST
```

Quaz must never automatically sell a pre-existing investment holding.

Only a position explicitly created by Quaz automated trading is eligible for Quaz automated profit-only exit management.

If state is ambiguous, do not trade.

---

# 30. Trading Days and Timing

Create a dedicated Trading Schedule Engine.

Default:

```text
Monday-Friday
```

Configurable entry windows:

```text
09:20-11:30
13:30-15:00
```

Separate:

```text
Market Monitoring Window
```

from:

```text
New Entry Window
```

Existing positions can continue to be monitored after the new-entry cutoff.

States:

```text
PRE_MARKET
MARKET_OPEN
ENTRY_WINDOW_OPEN
ENTRY_WINDOW_CLOSED
MARKET_CLOSED
HOLIDAY
USER_BLACKOUT
SCHEDULE_DISABLED
TIME_UNCERTAIN
```

Only ENTRY_WINDOW_OPEN permits new automated entries.

Use:

```text
Asia/Kolkata
```

for Indian market schedule calculations.

---

# 31. Exchange Holidays

Use a refreshable current exchange holiday calendar.

Store:

- exchange
- year
- date
- holiday name
- session type
- source
- last updated

Support:

- NSE
- BSE where applicable
- special sessions
- extraordinary closures
- changed timings
- Muhurat/special sessions where applicable

If the application cannot reliably determine whether the exchange is open:

```text
BLOCK NEW TRADES
```

---

# 32. STOP SCALPING

STOP SCALPING must be available from:

1. Dashboard
2. Trading screen
3. Persistent Android notification
4. Lock-screen notification where Android permits

When activated:

```text
automatedTradingEnabled = false
newEntriesAllowed = false
```

Immediately stop:

- new automated BUY execution
- new BUY GTT creation

Cancel eligible pending entry instructions where safely possible.

DO NOT automatically sell existing positions.

Existing Quaz positions remain monitored.

STOP state persists after:

- app restart
- backend restart
- Android restart
- phone reboot

Never automatically resume.

Resume requires:

1. Explicit user action
2. Biometric authentication
3. Portfolio synchronization
4. Broker synchronization
5. Full safety checks

---

# 33. Emergency Stop

Emergency Stop:

- immediately blocks new entries
- cancels eligible pending entry instructions where safely possible
- disables automation
- persists stopped state
- sends notification
- does not automatically sell positions

Manual emergency exit must be separate and clearly warn that it can realize a loss.

---

# 34. Capital Protection

Configurable:

- total scalping capital
- maximum capital per trade
- maximum capital per stock
- maximum concurrent positions
- maximum daily trades
- maximum daily deployed capital
- minimum expected net profit
- minimum risk/reward
- minimum technical score
- maximum holding duration
- allowed trading windows

RiskEngine owns these limits.

Claude cannot override them.

---

# 35. Maximum Holding Duration

Configure:

```text
maximum holding duration
```

When reached and position is not profitable:

```text
DO NOT AUTOMATICALLY SELL AT A LOSS.
```

Instead:

```text
POSITION WAITING FOR PROFIT
```

Notify the user.

Manual emergency exit remains available.

---

# 36. Market Data Failure

If:

- WebSocket disconnects
- quotes become stale
- price is invalid
- data is inconsistent
- broker connection fails
- market status is unknown

then:

```text
BLOCK NEW ENTRIES
```

Do not blindly retry after an API timeout.

First reconcile broker state.

---

# 37. Order Safety

Every order/GTT must have:

- unique internal trade ID
- idempotency protection
- duplicate protection
- instrument validation
- quantity validation
- capital validation
- current-state validation

If an API timeout occurs:

```text
DO NOT BLINDLY RETRY
```

First check Zerodha order/GTT state.

---

# 38. Partial Fills

Handle:

- partial fills
- actual average price
- actual quantity
- rejected orders
- cancelled orders
- modified orders
- GTT trigger status

Profit calculations must use actual executed values.

---

# 39. AI Failure

If Claude:

- times out
- returns invalid JSON
- returns missing fields
- returns contradictory data
- appears to hallucinate data

then:

```text
NO_TRADE
```

Never fall back to uncontrolled order execution.

---

# 40. Prompt Versioning

Store separate versioned prompts:

```text
stock_suggestion_v1
fundamental_analysis_v1
market_regime_v1
chart_analysis_v1
pre_buy_v1
buy_gtt_validation_v1
sell_analysis_v1
sell_gtt_validation_v1
psychology_guard_v1
```

Store:

- prompt version
- model
- timestamp
- input snapshot ID
- AI response
- decision
- RiskEngine result

---

# 41. Audit Log

For every trade proposal record:

- timestamp
- symbol
- market snapshot
- indicator snapshot
- fundamental snapshot
- Claude model
- prompt version
- AI response
- AI score
- RiskEngine checks
- rejection reason
- GTT parameters
- Zerodha response
- execution result
- realized P&L

Never store secrets.

---

# 42. Paper Trading

Modes:

```text
ANALYSIS
PAPER
LIVE_ARMED
LIVE_ACTIVE
STOPPED
```

Paper mode must simulate:

- entries
- GTTs
- fills
- charges
- slippage
- profit
- loss
- holding duration

No real Zerodha order may be submitted in PAPER mode.

---

# 43. Live Activation

Require:

1. Explicit LIVE activation
2. Biometric authentication
3. Portfolio synchronization
4. Broker connection check
5. Capital check
6. Risk configuration check
7. Whitelist validation
8. Trading schedule validation
9. Fresh market data
10. STOP SCALPING is not active

Default:

```text
ANALYSIS
```

---

# 44. Fundamental vs Current Timing Display

Show separately.

## Fundamental Quality

```text
Quality       91
Growth        94
Valuation     72
Balance Sheet 88
Cash Flow     93
Management    90
Governance    88
```

## Current Entry Quality

```text
Market        76
Sector        84
Technical     91
Momentum      88
Volume        92
Entry         87
Risk/Reward   86
```

Example:

```text
FUNDAMENTAL QUALITY: 94
CURRENT ENTRY QUALITY: 38

GOOD COMPANY
BAD ENTRY

ACTION: WAIT
```

---

# 45. Psychology Guard

Before recommending an entry, identify:

- FOMO
- chasing
- revenge trading
- averaging down
- overtrading
- emotional target changes
- buying after extended movement
- panic selling

Example:

```text
FOMO WARNING

Price has moved significantly above the validated
entry zone.

WAIT for a better setup.
```

---

# 46. AI Output Must Be Structured

Do not parse arbitrary natural-language AI responses for order execution.

Use structured JSON/schema validation.

Example:

```text
Claude
↓
JSON Schema Validation
↓
Business Rule Validation
↓
RiskEngine
↓
GTT Engine
```

If schema validation fails:

```text
NO_TRADE
```

---

# 47. Security

Use:

- HTTPS/TLS
- secure backend secrets
- Android Keystore for local secrets
- biometric authentication
- encrypted local storage
- backend authentication
- authorization
- audit logging
- rate limiting
- API request validation
- secret redaction
- no credentials in logs

Claude must never receive:

- Zerodha API secret
- broker password
- TOTP
- private authentication tokens

---

# 48. UI Configuration

Settings should contain:

## AI

- Claude model
- API connection status
- prompts
- prompt versions
- analysis frequency

## Broker

- Zerodha connection
- API configuration
- connection status

## Security

- fingerprint/biometric
- protected settings
- session security

## Trading

- trading days
- entry windows
- market monitoring window
- holiday handling
- personal blackout dates

## Capital

- total capital
- per-trade limit
- per-stock limit
- daily limit

## Risk

- minimum technical score
- minimum risk/reward
- minimum net profit
- maximum holding duration
- profit-only selling

## Universe

- whitelist
- blocklist
- suggested stocks

## Notifications

- trade alerts
- GTT alerts
- profit alerts
- rejection alerts
- holiday alerts
- broker disconnect alerts

---

# 49. Dashboard

Show:

```text
ZERODHA
🟢 Connected

MARKET
🟢 OPEN

AUTOMATION
🟢 ACTIVE

WHITELIST
XX stocks

ELIGIBLE TODAY
XX

AI OPPORTUNITIES
XX

QUAZ POSITIONS
XX

CAPITAL
₹XX,XXX

DEPLOYED
₹XX,XXX

NET P&L
₹XX,XXX
```

Large button:

```text
🛑 STOP SCALPING
```

---

# 50. Opportunity Screen

For each eligible stock:

```text
SYMBOL

Fundamental       88
Technical         91
Market            82
Sector            86
Entry             89

Trend: Bullish
VWAP: Above
Volume: Strong
RSI: 64
R/R: 2.3

Entry: ₹XXX
Target: ₹XXX
Expected Net Profit: ₹XXX

STATUS:
BUY CANDIDATE
```

Allow:

```text
ANALYSE
VIEW CHART
VIEW FUNDAMENTALS
```

Do not allow any path to bypass RiskEngine.

---

# 51. Positions Screen

Separate:

```text
PRE-EXISTING HOLDINGS

QUAZ AUTOMATED POSITIONS
```

For Quaz positions display:

- entry
- average price
- current price
- gross P&L
- estimated net P&L
- minimum profitable exit
- recommended target
- GTT state
- holding duration
- AI status
- trend

---

# 52. Profit-Only Sell Display

Example:

```text
Entry:
₹500

Current:
₹510

Estimated Charges:
₹2

Minimum Profitable Exit:
₹507

Estimated Net Profit:
₹8

AI:
HOLD / TARGET ₹515

SELL GTT:
₹515
```

Loss scenario:

```text
Entry:
₹500

Current:
₹493

Minimum Profitable Exit:
₹507

Estimated Net P&L:
LOSS

SELL:
BLOCKED

Reason:
Profit-only protection active
```

---

# 53. Final Automated BUY Flow

```text
USER WHITELIST
        ↓
EXISTING HOLDING CHECK
        ↓
MARKET STATUS
        ↓
TRADING DAY
        ↓
TRADING TIME
        ↓
MARKET REGIME
        ↓
SECTOR ANALYSIS
        ↓
FUNDAMENTAL QUALITY
        ↓
TECHNICAL ANALYSIS
        ↓
ENTRY QUALITY
        ↓
RISK/REWARD
        ↓
EXPECTED NET PROFIT
        ↓
CAPITAL LIMITS
        ↓
DUPLICATE CHECK
        ↓
BROKER HEALTH
        ↓
STOP SCALPING CHECK
        ↓
RISK ENGINE
        ↓
BUY GTT
```

---

# 54. Final Automated SELL Flow

```text
QUAZ POSITION
        ↓
CURRENT MARKET DATA
        ↓
TECHNICAL ANALYSIS
        ↓
MARKET / SECTOR ANALYSIS
        ↓
NET PROFIT CALCULATION
        ↓
MINIMUM PROFITABLE EXIT
        ↓
SELL TARGET
        ↓
PROFIT-ONLY CHECK
        ↓
RISK ENGINE
        ↓
SELL GTT
```

---

# 55. Absolute Rules

1. No F&O.
2. No futures.
3. No options.
4. No leverage.
5. No short selling.
6. Only user-approved whitelist stocks.
7. AI cannot automatically add stocks to whitelist.
8. Existing investment holdings are excluded from automated scalping.
9. Never automatically sell pre-existing investment holdings.
10. Only Quaz-generated positions are eligible for Quaz automated profit-only exits.
11. No BUY without current market analysis.
12. No BUY based only on fundamentals.
13. No BUY based only on technical indicators.
14. Fundamental quality and current entry quality must both be acceptable.
15. No BUY when market data is stale.
16. No BUY when market status is uncertain.
17. No BUY outside configured trading windows.
18. No BUY on exchange holidays.
19. No duplicate GTTs.
20. Never exceed capital limits.
21. Never exceed daily trade limits.
22. Include applicable charges in profitability.
23. Never automatically sell below minimum profitable exit.
24. Claude cannot override RiskEngine.
25. Claude cannot execute orders.
26. Claude cannot modify protected rules.
27. STOP SCALPING blocks new automated entries.
28. STOP SCALPING does not automatically sell existing positions.
29. STOP state persists after restart/reboot.
30. If Claude is uncertain, WAIT.
31. If data is missing, NO_TRADE.
32. If broker state is unknown, NO_TRADE.
33. If order state is unknown, reconcile before retry.
34. Capital preservation has priority over trade frequency.
35. Profit is never guaranteed.
36. No trade is better than a bad trade.
37. Never parse unvalidated natural-language AI output directly into an order.
38. Never expose AI or broker credentials.
39. Never allow AI to bypass deterministic safety controls.
40. Never automatically convert an AI suggestion into a GTT without RiskEngine approval.

---

# 56. Final Principle

The system must NEVER operate like:

```text
Claude says BUY
       ↓
BUY
```

It must operate like:

```text
Claude says BUY_CANDIDATE
       ↓
Validate AI JSON
       ↓
RiskEngine
       ↓
GTT Validation
       ↓
Zerodha
```

And:

```text
Claude says SELL_PROFIT_CANDIDATE
       ↓
Calculate actual net P&L
       ↓
Minimum profitable exit check
       ↓
RiskEngine
       ↓
GTT Validation
       ↓
Zerodha
```

## Final Quaz principle

> **AI provides intelligence. Deterministic software provides discipline. Zerodha provides execution.**

No component may bypass the safety layer.
