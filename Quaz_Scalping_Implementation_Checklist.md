# Quaz Scalping — Implementation Checklist

Derived from `Quaz_Scalping_Final_AI_GTT_Prompt.md`. Use this to track build progress. Check items off as they are implemented **and** verified.

---

## 1. Foundational Architecture

- [ ] Flutter Android app (Dashboard/Config) built and communicating with backend only over HTTPS
- [ ] .NET backend hosts: Market Data, Technical Engine, Fundamental Engine, Schedule Engine, Claude AI Gateway, Risk Engine, GTT Engine, Position Manager
- [ ] Backend runs continuously independent of phone lock/connection state
- [ ] Claude API and Zerodha Kite API integrated only via the backend (no direct client access from Android)
- [ ] Android provides: status monitoring, STOP SCALPING, notifications, configuration, biometric auth, trade history, portfolio info

## 2. Trading Scope Guardrails

- [ ] Hard block: no Futures, Options, F&O, leverage, short selling
- [ ] Only approved/whitelisted equities tradeable
- [ ] No auto discovery-to-trade without explicit user approval
- [ ] No automated averaging down
- [ ] No automated loss-making exits
- [ ] No UI/marketing text implies guaranteed profit; app describes itself as "short-term/GTT-based trading"

## 3. Claude API Configuration & Secrets

- [ ] `ANTHROPIC_API_KEY` stored as backend secret only (never in Git, APK, logs, notifications, or sent to Claude)
- [ ] Android app never has AI or broker credentials
- [ ] AI Configuration screen: provider, model selector, masked API key, connection status, Test Connection, Replace API Key
- [ ] Stored key never re-displayed after save
- [ ] Configurable prompts: Stock Suggestion, Fundamental Analysis, Market Analysis, Chart Analysis, Pre-Buy, Buy GTT Validation, Sell Analysis, Sell GTT Validation, Psychology
- [ ] Each prompt supports: default, custom override, reset-to-default, version tracking, enabled/disabled toggle
- [ ] Protected settings gated behind biometric authentication

## 4. AI Responsibility Boundaries

- [ ] Claude restricted to analysis/suggestion only (no order/GTT create/modify/cancel, no limit/whitelist/blocklist changes, no RiskEngine override, no broker credential access, no return guarantees)
- [ ] Stale/missing/contradictory/unreliable data → forced `NO_TRADE`

## 5. Fundamental Analysis Engine (X/Y/Z + S-C-R-A-P)

- [ ] X-Axis (Quality) analysis implemented
- [ ] Y-Axis (Growth, multi-year trend) analysis implemented
- [ ] Z-Axis (Valuation) analysis implemented — never "cheap" from low P/E alone
- [ ] S — Sector analysis (15% CAGR treated as positive signal, not requirement)
- [ ] C — Compounding engines analysis (25% YoY treated as positive signal, not requirement)
- [ ] R — Red flags (debt, cash flow, working capital, promoters, governance) → severe flags force `REJECT`
- [ ] A — Allocation logic scoped to long-term view only (15–20 stock ideal), not mixed into short-term automated universe
- [ ] P — Psychology detection (FOMO, revenge trading, chasing, averaging down, overtrading, panic selling, emotional target changes)
- [ ] Investor-style lenses (Buffett, Peter Lynch, Growth+Quality, Growth+Valuation) implemented as analytical frames only
- [ ] Fundamental Score computed with default weights (Quality 25/Growth 25/Valuation 20/Balance Sheet 10/Cash Flow 10/Management 5/Governance 5), returns all sub-scores 0–100
- [ ] Trade never triggered from fundamental score alone
- [ ] Global Supply Chain analysis implemented
- [ ] Management Track Record analysis implemented
- [ ] Future Order Book analysis implemented (large order book not auto-positive)

## 6. Stock Suggestion Flow

- [ ] Stock Suggestion Prompt implemented per spec (default prompt text, full analysis checklist, classification: STRONG_CANDIDATE/CANDIDATE/WATCH/AVOID/REJECT)
- [ ] Suggested stocks never auto-added to whitelist
- [ ] User approval UI: Add to Whitelist / Ignore actions only

## 7. Market & Technical Analysis

- [ ] Market-Regime analysis (NIFTY/SENSEX/NASDAQ/S&P/breadth/volatility/sector/crude/yields/currency/macro) → STRONG_BULLISH…STRONG_BEARISH classification
- [ ] Highly uncertain market conditions → `NO_TRADE`
- [ ] Technical Engine computes indicators server-side (OHLC, volume, VWAP, EMA 9/20/50, RSI, MACD, ATR, rel. volume, volatility, spread, liquidity) across 1m/3m/5m/15m/30m
- [ ] Price structure detection (HH/HL/LH/LL, support/resistance, breakout, retest, false breakout, gap, candle confirmation, momentum)
- [ ] Claude only interprets supplied technical values — never invents them
- [ ] Multi-indicator confirmation required (no single-indicator trades)
- [ ] Chart Analysis Prompt implemented per spec, returns regime + ENTRY_NOW/WAIT/AVOID + entry/target zones + invalidation + risk/reward
- [ ] Technical Score computed with default weights (Market Regime 15/Trend 15/VWAP 15/Volume 15/Momentum 10/EMA Alignment 10/S-R 10/Liquidity 5/Volatility 5), default minimum 75/100, configurable threshold

## 8. Pre-BUY Analysis & Validation

- [ ] Pre-BUY analysis combines fundamental + market + technical inputs
- [ ] Determines entry zone, preferred entry, target, risk/reward, holding period, confidence, invalidation conditions
- [ ] Decisions limited to: BUY_CANDIDATE / WAIT_FOR_ENTRY / NO_TRADE / REJECT
- [ ] BUY_CANDIDATE requires ALL of: whitelisted + acceptable market + acceptable sector + strong technical setup + acceptable R/R + net profit after charges + all RiskEngine checks pass
- [ ] Good company + poor entry → `WAIT` (not skipped/rejected outright)
- [ ] Pre-BUY JSON schema implemented exactly as specified and schema-validated

## 9. RiskEngine — Final BUY Validation (28-point checklist)

- [ ] 1. Whitelist membership
- [ ] 2. Not a pre-existing holding
- [ ] 3. Not already a Quaz position
- [ ] 4. No duplicate pending order
- [ ] 5. No duplicate GTT
- [ ] 6. Market open check
- [ ] 7. Valid trading day
- [ ] 8. Not an exchange holiday
- [ ] 9. Inside allowed entry window
- [ ] 10. STOP SCALPING is OFF
- [ ] 11. Automation active
- [ ] 12. Market data freshness check
- [ ] 13. Zerodha connection healthy
- [ ] 14. Broker session valid
- [ ] 15. Capital available
- [ ] 16. Per-trade limit check
- [ ] 17. Per-stock limit check
- [ ] 18. Total capital limit check
- [ ] 19. Daily trade limit check
- [ ] 20. Concurrent-position limit check
- [ ] 21. Quantity validity
- [ ] 22. Instrument validity
- [ ] 23. Expected net profit exceeds charges
- [ ] 24. Technical score meets minimum
- [ ] 25. Risk/reward meets minimum
- [ ] 26. User blocklist check
- [ ] 27. System blocklist check
- [ ] 28. Latest price still within validated entry conditions
- [ ] Any failed check → `REJECT` with recorded reason; Claude cannot override

## 10. BUY GTT Construction & Execution

- [ ] Claude supplies entry analysis/preferred entry/target/confidence/reasoning only
- [ ] App calculates quantity, capital, trigger, limit price, charges, expected net profit
- [ ] GTT Engine performs final validation before submission
- [ ] Only GTT Engine may send requests to Zerodha (no other component)

## 11. Profit Calculation & Profit-Only Selling

- [ ] Profit never computed as simple `LTP > Entry Price`
- [ ] Net P&L formula implemented: Gross Exit Value − Entry Cost − Brokerage − Exchange Charges − Taxes − Other Transaction Costs
- [ ] Correct current Zerodha charge schedule used per product/exchange
- [ ] `minimum_profitable_exit_price` calculated (entry cost + exit charges + taxes/fees + configured min profit)
- [ ] `estimated_net_profit <= 0` → `DO_NOT_SELL`
- [ ] `estimated_net_profit < minimum_required_profit` (if configured) → `DO_NOT_SELL`
- [ ] Claude cannot override profit-only rule

## 12. SELL Analysis & GTT Validation

- [ ] SELL Analysis Prompt implemented per spec with full input/analysis list
- [ ] Decisions limited to: SELL_PROFIT_CANDIDATE / HOLD_FOR_HIGHER_PROFIT / HOLD / WAIT / DO_NOT_SELL
- [ ] Net-loss proposed exit → forced `DO_NOT_SELL`
- [ ] SELL JSON schema implemented exactly as specified and schema-validated
- [ ] SELL GTT created only when Recommended Sell Price > Minimum Profitable Exit AND position is Quaz-generated/open/quantity-correct/no conflicting order/valid market data/valid GTT params/profit-only check passes
- [ ] Otherwise → `DO_NOT_SELL`

## 13. Holdings Protection & Universe Management

- [ ] Sync Zerodha Holdings + Positions + Pending Orders before every automated BUY
- [ ] Effective automated universe = Whitelist − Pre-existing Holdings − Pre-existing Positions − User Blocklist − System Blocklist
- [ ] Quaz never auto-sells a pre-existing (non-Quaz) holding
- [ ] Only Quaz-created positions eligible for automated profit-only exit management
- [ ] Ambiguous state → no trade

## 14. Trading Schedule Engine

- [ ] Configurable trading days (default Mon–Fri)
- [ ] Configurable entry windows (default 09:20–11:30, 13:30–15:00)
- [ ] Market Monitoring Window kept separate from New Entry Window
- [ ] Existing positions continue to be monitored after entry cutoff
- [ ] State machine implemented: PRE_MARKET, MARKET_OPEN, ENTRY_WINDOW_OPEN, ENTRY_WINDOW_CLOSED, MARKET_CLOSED, HOLIDAY, USER_BLACKOUT, SCHEDULE_DISABLED, TIME_UNCERTAIN
- [ ] Only `ENTRY_WINDOW_OPEN` permits new automated entries
- [ ] All schedule calculations use `Asia/Kolkata` timezone

## 15. Exchange Holiday Handling

- [ ] Refreshable exchange holiday calendar store (exchange, year, date, name, session type, source, last updated)
- [ ] Supports NSE, BSE where applicable, special sessions, extraordinary closures, changed timings, Muhurat sessions
- [ ] Undeterminable market-open status → block new trades

## 16. STOP SCALPING & Emergency Stop

- [ ] STOP SCALPING accessible from: Dashboard, Trading screen, persistent Android notification, lock-screen notification (where permitted)
- [ ] Activation sets `automatedTradingEnabled=false` and `newEntriesAllowed=false`
- [ ] Immediately halts new automated BUY execution and new BUY GTT creation
- [ ] Cancels eligible pending entry instructions where safely possible
- [ ] Never auto-sells existing positions; existing Quaz positions remain monitored
- [ ] STOP state persists across app/backend/Android restart and phone reboot
- [ ] Never auto-resumes; resume requires explicit user action + biometric auth + portfolio sync + broker sync + full safety checks
- [ ] Emergency Stop: blocks new entries, cancels eligible pending entries, disables automation, persists state, sends notification, does not auto-sell
- [ ] Manual emergency exit is a separate, clearly-labeled action warning it can realize a loss

## 17. Capital & Duration Protection

- [ ] Configurable: total scalping capital, max per-trade capital, max per-stock capital, max concurrent positions, max daily trades, max daily deployed capital, min expected net profit, min risk/reward, min technical score, max holding duration, allowed trading windows
- [ ] RiskEngine owns all limits; Claude cannot override
- [ ] Max holding duration reached + unprofitable → status `POSITION WAITING FOR PROFIT` (never auto-sell at a loss)
- [ ] User notified when this occurs; manual emergency exit remains available

## 18. Resilience & Data Integrity

- [ ] Market data failure (WebSocket disconnect, stale quotes, invalid price, inconsistent data, broker failure, unknown market status) → block new entries
- [ ] No blind retry after API timeout; broker state reconciled first
- [ ] Every order/GTT has: unique internal trade ID, idempotency protection, duplicate protection, instrument/quantity/capital/current-state validation
- [ ] Partial fills, rejected/cancelled/modified orders, and GTT trigger status all handled; P&L uses actual executed values
- [ ] Claude failure modes (timeout, invalid JSON, missing fields, contradictory data, suspected hallucination) → `NO_TRADE`, never fallback to uncontrolled execution

## 19. Prompt Versioning & Audit Log

- [ ] Versioned prompt storage: stock_suggestion_v1, fundamental_analysis_v1, market_regime_v1, chart_analysis_v1, pre_buy_v1, buy_gtt_validation_v1, sell_analysis_v1, sell_gtt_validation_v1, psychology_guard_v1
- [ ] Each stored with: prompt version, model, timestamp, input snapshot ID, AI response, decision, RiskEngine result
- [ ] Audit log records per trade proposal: timestamp, symbol, market/indicator/fundamental snapshots, Claude model, prompt version, AI response, AI score, RiskEngine checks, rejection reason, GTT parameters, Zerodha response, execution result, realized P&L
- [ ] Audit log never stores secrets

## 20. Modes & Live Activation

- [ ] Mode states implemented: ANALYSIS, PAPER, LIVE_ARMED, LIVE_ACTIVE, STOPPED (default = ANALYSIS)
- [ ] PAPER mode simulates entries, GTTs, fills, charges, slippage, profit/loss, holding duration — no real order ever submitted
- [ ] LIVE activation requires: explicit activation + biometric auth + portfolio sync + broker connection check + capital check + risk config check + whitelist validation + trading schedule validation + fresh market data + STOP SCALPING inactive

## 21. AI Output Handling

- [ ] All AI responses validated via strict JSON schema (no free-text parsing into orders)
- [ ] Pipeline enforced: Claude → JSON Schema Validation → Business Rule Validation → RiskEngine → GTT Engine
- [ ] Schema validation failure → `NO_TRADE`

## 22. Security

- [ ] HTTPS/TLS everywhere
- [ ] Backend secrets stored securely (not in code/Git)
- [ ] Android Keystore used for local secrets
- [ ] Biometric authentication for protected actions
- [ ] Encrypted local storage on device
- [ ] Backend authentication & authorization enforced
- [ ] Audit logging, rate limiting, API request validation implemented
- [ ] Secret redaction; no credentials ever in logs
- [ ] Claude never receives Zerodha API secret, broker password, TOTP, or private auth tokens

## 23. UI / Screens

- [ ] Settings: AI (model, connection status, prompts/versions, analysis frequency)
- [ ] Settings: Broker (Zerodha connection, API config, status)
- [ ] Settings: Security (biometric, protected settings, session security)
- [ ] Settings: Trading (days, entry windows, monitoring window, holidays, personal blackout dates)
- [ ] Settings: Capital (total, per-trade, per-stock, daily limits)
- [ ] Settings: Risk (min technical score, min R/R, min net profit, max holding duration, profit-only selling)
- [ ] Settings: Universe (whitelist, blocklist, suggested stocks)
- [ ] Settings: Notifications (trade/GTT/profit/rejection/holiday/broker-disconnect alerts)
- [ ] Dashboard shows: Zerodha status, market status, automation status, whitelist count, eligible-today count, AI opportunities count, Quaz positions count, capital, deployed capital, net P&L
- [ ] Dashboard has large, always-visible STOP SCALPING button
- [ ] Opportunity screen: per-stock scores (fundamental/technical/market/sector/entry), trend/VWAP/volume/RSI/R-R, entry/target/expected net profit, status, actions (Analyse/View Chart/View Fundamentals) — no action bypasses RiskEngine
- [ ] Positions screen: separates Pre-Existing Holdings from Quaz Automated Positions; Quaz positions show entry/avg/current price, gross & net P&L, min profitable exit, target, GTT state, holding duration, AI status, trend
- [ ] Profit-only sell display shows entry/current/charges/min profitable exit/net profit/AI target/GTT, and blocked-with-reason view for loss scenarios
- [ ] Fundamental vs Current Entry Quality shown separately (e.g., "GOOD COMPANY / BAD ENTRY / ACTION: WAIT")
- [ ] Psychology Guard warnings surfaced in UI (e.g., FOMO warning banner)

## 24. End-to-End Flow Verification

- [ ] Automated BUY flow follows exact order: Whitelist → Existing Holding Check → Market Status → Trading Day → Trading Time → Market Regime → Sector Analysis → Fundamental Quality → Technical Analysis → Entry Quality → Risk/Reward → Expected Net Profit → Capital Limits → Duplicate Check → Broker Health → STOP SCALPING Check → RiskEngine → BUY GTT
- [ ] Automated SELL flow follows exact order: Quaz Position → Current Market Data → Technical Analysis → Market/Sector Analysis → Net Profit Calculation → Minimum Profitable Exit → Sell Target → Profit-Only Check → RiskEngine → SELL GTT
- [ ] Confirm no code path allows "Claude says BUY" → BUY directly (must always route through JSON validation → RiskEngine → GTT Validation → Zerodha)

## 25. Absolute Rules Sign-off (final regression checklist)

- [ ] 1–5: No F&O / futures / options / leverage / short selling
- [ ] 6–7: Only whitelisted stocks; AI cannot add to whitelist
- [ ] 8–10: Existing holdings excluded from scalping; never auto-sold; only Quaz positions eligible for auto profit-only exit
- [ ] 11–14: No BUY without full market analysis; not from fundamentals alone; not from technicals alone; both fundamental and entry quality must pass
- [ ] 15–18: No BUY on stale data, uncertain market status, outside trading windows, or on holidays
- [ ] 19–21: No duplicate GTTs; capital limits enforced; daily trade limits enforced
- [ ] 22–23: Charges included in profitability; never sell below minimum profitable exit
- [ ] 24–26: Claude cannot override RiskEngine, execute orders, or modify protected rules
- [ ] 27–29: STOP SCALPING blocks new entries only (no auto-sell); state persists across restarts
- [ ] 30–34: Uncertainty → WAIT; missing data → NO_TRADE; unknown broker/order state → reconcile before retry; capital preservation prioritized
- [ ] 35–36: No profit guarantees anywhere in app; "no trade is better than a bad trade" reflected in UX copy
- [ ] 37–40: No unvalidated NL parsing into orders; no credential exposure; AI cannot bypass deterministic controls; no auto-conversion of AI suggestion to GTT without RiskEngine approval

---

**Final principle to re-verify before any release:**
AI provides intelligence → Deterministic RiskEngine provides discipline → Zerodha provides execution. No component may bypass the safety layer.
