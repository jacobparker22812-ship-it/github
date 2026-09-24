---
name: robinhood-weekly-checkin
description: Produce the standard portfolio check-in for the user's Robinhood Agentic account. Use when the user asks how their investments are doing, asks for a check-in, portfolio update or weekly report, or when a scheduled check-in fires. Read-only; never places orders.
---

# Weekly portfolio check-in

This is a read-only report. Never place or cancel orders here. If action
looks worthwhile, suggest it and let the user decide.

## Steps

1. `get_accounts`: find the account nicknamed "Agentic".
2. `get_portfolio` for that account: total value, cash, buying power,
   pending deposits.
3. `get_equity_positions`: symbol, quantity, average cost.
   `get_crypto_positions` (with `rhs_account_number`): coins held and
   cost basis.
4. `get_equity_quotes` for every held fund and `get_crypto_quotes`
   (use `mark_price`) for every held coin. Say what time prices are
   from.
5. `get_equity_orders` and `get_crypto_orders` since the last check-in
   (default: the last 7 days): list fills, and flag anything still
   queued.
6. If `investing/trade-log.md` exists in the repo, read it to find total
   money deposited and past trades.

## Report format

Keep it short. Use this layout:

**Account value:** $X (cash $Y)
**Since last check-in:** +/-$Z (+/-N%)
**Since you started:** value vs. total deposited, +/-$ and %

| Holding | Amount | Value | Cost | Gain/loss | Weight | Target |
|---|---|---|---|---|---|---|
| VTI | ... shares | ... | ... | ... | ...% | 65% |
| VXUS | ... shares | ... | ... | ... | ...% | 20% |
| BTC | ... BTC | ... | ... | ... | ...% | 7% |
| ETH | ... ETH | ... | ... | ... | ...% | 3% |
| Cash | | ... | | | ...% | 5% |

Crypto amounts are coins, never "shares".

Then, only if they apply:

- **Drift:** any holding more than 5 points off target, and the
  suggested fix (prefer buying the underweight fund with new cash).
- **Crypto cap:** if BTC + ETH are above 10% of the account, say so
  and suggest trimming back to target (the user decides).
- **Cash waiting:** uninvested cash above the 5% buffer, and a suggested
  split using the robinhood-deposit skill.
- **Open orders:** anything queued or partially filled.
- **Upcoming:** market holidays this week, if known.

End with one plain sentence on the big picture. Never predict returns.
A down week is normal; say so calmly rather than suggesting selling.

## Scheduling

To get this automatically, the user can set up a weekly Routine (for
example Mondays at 10 AM ET) with the prompt: "Run the
robinhood-weekly-checkin skill and send me the report."
