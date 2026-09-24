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
| VTI (Vanguard Total Stock Market ETF) | 70% | Broad US stocks, ~0.03% fee |
| VXUS (Vanguard Total International Stock ETF) | 25% | Non-US stocks, ~0.05% fee |
| Cash | 5% | Buffer for rounding and dips |

- Favor broad, low-cost index funds over single stocks, crypto,
  options, leverage, or short-term trading.
- New deposits: split them by the target percentages above.
- Rebalance only when a holding drifts more than 5 percentage points
  from its target, and prefer rebalancing with new money over selling.
- Do not sell in response to short-term drops. Hold for the long term.
- Never use margin. Keep buys within cash buying power.

## Placing orders

1. Check buying power with `get_portfolio` and quotes with
   `get_equity_quotes`.
2. Check the market is open. Dollar-based and fractional orders only
   work in regular hours (9:30 AM to 4:00 PM ET); placed outside that
   window they queue for the next open. Say so.
3. Preview every order with `review_equity_order`.
4. Show the user the plan, any `order_checks` alerts, and the
   `market_data_disclosure` text verbatim.
5. Get an explicit "yes" before calling `place_equity_order`.
6. Use a fresh UUID `ref_id` per order (for example from
   `cat /proc/sys/kernel/random/uuid`) and reuse it only when retrying
   the same order.
7. Report each order's state honestly: queued or placed is not filled.

## Check-ins

When asked how things are going: show positions, current value, gain or
loss versus cost, and whether the mix has drifted from the targets.
Never promise returns; all investing can lose money.
