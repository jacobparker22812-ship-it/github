---
name: robinhood-investing
description: House rules for managing the user's Robinhood "Agentic" account through the Robinhood-Trading tools. Use whenever the user asks to invest, trade, rebalance, deploy new deposits, check how their money is doing, or otherwise act on their Robinhood account.
---

# Robinhood investing rules

The user has delegated investment choices for their **Agentic** account
(the only account these tools can trade). The goal is to grow the money
over the long term, always in the user's best interest.

## Accounts

- Trade **only** in the account nicknamed "Agentic" (the one
  `get_accounts` marks as tradable by the agent). Look up its
  account number with `get_accounts`; never hard-code it.
- The default individual account and the Roth IRA are **view-only**.
  Never try to trade in them.
- Mask account numbers to the last 4 digits when showing them.

## Strategy

Default target allocation for invested money:

| Holding | Target | Why |
|---|---|---|
| VTI (Vanguard Total Stock Market ETF) | 65% | Broad US stocks, ~0.03% fee |
| VXUS (Vanguard Total International Stock ETF) | 20% | Non-US stocks, ~0.05% fee |
| BTC (Bitcoin) | 7% | Largest, most established crypto |
| ETH (Ethereum) | 3% | Second-largest crypto |
| Cash | 5% | Buffer for rounding and dips |

- Crypto is capped at **10%** of the account in total. It can fall
  50-80% in a bad year, so it stays a small satellite, not the core.
- Only BTC and ETH. No other coins, meme coins, or tokens.
- Favor broad, low-cost index funds over single stocks, options,
  leverage, or short-term trading.
- Crypto trades 24/7 and is bought through the crypto tools
  (`preview_crypto_order`, `place_crypto_order`), which take the
  account's `rhs_account_number`, not `rhc_account_number`. Crypto
  amounts are coins, never "shares".
- Crypto prices include a buy/sell spread (often 1-2%). Check
  `get_crypto_quotes` and mention the spread before buying.
- New deposits: split them by the target percentages above, following
  the dollar-cost averaging rules below.
- Rebalance only when a holding drifts more than 5 percentage points
  from its target, and prefer rebalancing with new money over selling.
- Do not sell in response to short-term drops. Hold for the long term.
- Never use margin. Keep buys within cash buying power.

## Dollar-cost averaging

All buying follows dollar-cost averaging (DCA): fixed amounts, on a
regular schedule, whatever the price.

- **Schedule:** the user deposits about **$200 every two weeks**. Buys
  happen only when a scheduled deposit arrives, and are invested that
  same week.
- **No timing:** never delay, skip, shrink or enlarge a scheduled buy
  because prices are up, down, or because of news or indicators (RSI,
  MACD, moving averages). Indicators are context only.
- **No extra buys between deposits.** Do not buy dips with the cash
  buffer or add unscheduled purchases.
- **Lump sums:** if a deposit is more than twice the usual amount (over
  $400), do not invest it all at once. Split it into equal installments
  of about $200, added to the next scheduled buys, and hold the rest in
  cash until then. Keep a schedule of the pending installments in
  `investing/trade-log.md`.
- **Missed or late deposits:** just invest whatever arrives on the next
  cycle. Do not catch up with extra buys.
- If the user asks for an off-schedule buy, remind them it breaks the
  DCA plan and proceed only if they confirm in their own words.

## Strategy reviews and adding holdings

The user wants to keep accumulating the current holdings, and to add
other holdings if the plan stops working for them. Review the plan on a
schedule, never in reaction to a bad week.

**When to review:** every 6 months, and when the account first passes
$2,500, $5,000 and $10,000. Outside those times, only if the user asks.

**What counts as "not working"** (judge over at least 12 months, not
weeks):

- A holding trails its own benchmark by more than 1% a year (for
  example VTI vs. the total US market). Poor results for the whole
  market are not a reason to change the plan.
- A holding's cost goes up, it closes, or a clearly cheaper equivalent
  appears.
- Crypto swings are too large for the user to stay comfortable holding.
- The user's goals, timeline or deposit amount change.
- The account has grown enough that one more building block adds real
  diversification.

**Candidates that make sense** as the account grows (all broad and
low-cost; research each with the robinhood-research skill first):

| Candidate | Role | Consider from |
|---|---|---|
| BND (Vanguard Total Bond Market) | Steadier ballast; cushions stock drops | $5,000+, or sooner if drops feel too hard |
| AVUV (small-cap value) | Tilt toward historically higher-returning stocks | $5,000+ |
| VNQ (US real estate) | Real estate exposure | $10,000+ |
| SCHD (dividend stocks) | Income tilt | If income becomes a goal |

Single stocks, sector bets, leveraged funds and other coins stay off
the list.

**How a change happens:**

1. Present the review: what is and isn't working, with numbers.
2. Propose the change: new holding, new target percentages (always
   adding to 100%), and why.
3. The user approves in their own words.
4. Update this table, the guardrails' allowed symbols, and the deposit
   targets together in one commit.
5. Move toward the new mix with new deposits (DCA), not by selling,
   unless the user asks to sell.

## Placing orders

1. Check buying power with `get_portfolio` and quotes with
   `get_equity_quotes`.
2. Check the market is open. Dollar-based and fractional orders only
   work in regular hours (9:30 AM to 4:00 PM ET); placed outside that
   window they queue for the next open. Say so.
3. Preview every order with `review_equity_order` (stocks and ETFs) or
   `preview_crypto_order` (crypto).
4. Show the user the plan, any `order_checks` alerts, and the
   `market_data_disclosure` text verbatim.
5. Get an explicit "yes" before calling `place_equity_order` or
   `place_crypto_order`.
6. Use a fresh UUID `ref_id` per order (for example from
   `cat /proc/sys/kernel/random/uuid`) and reuse it only when retrying
   the same order.
7. Report each order's state honestly: queued or placed is not filled.

## Check-ins

When asked how things are going: show positions, current value, gain or
loss versus cost, and whether the mix has drifted from the targets.
Never promise returns; all investing can lose money.
