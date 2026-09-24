---
name: robinhood-deposit
description: Invest new money that has arrived in the user's Robinhood Agentic account. Use when the user says they added or deposited money, asks to invest their cash, or a check-in finds cash above the buffer.
---

# Deposit handler

Follow the robinhood-guardrails skill for every order. The targets come
from the robinhood-investing skill: VTI 70%, VXUS 25%, cash 5%.

## Steps

1. `get_accounts`: find the account nicknamed "Agentic".
2. `get_portfolio`: note cash, buying power and pending deposits.
   Use buying power (not margin) as the spendable amount.
3. `get_equity_positions` and `get_equity_quotes`: current value of
   each holding.
4. Work out the split:
   - New total = current holdings value + cash.
   - Target value for each fund = new total x its target %.
   - Shortfall for each fund = target value - current value (never below 0).
   - Money to invest = cash - max($25, 5% of new total).
   - Spread the money to invest across the funds in proportion to
     their shortfalls. This rebalances without selling.
   - Skip any buy under $1 (Robinhood's minimum for dollar orders) and
     leave that money in cash.
5. Check the guardrails: daily buy limit, cash floor, allowed symbols.
6. Preview each buy with `review_equity_order` as a market order with
   `dollar_amount` and `market_hours: regular_hours`.
7. Show the user one table: fund, amount, estimated shares, and the
   resulting mix vs. targets. Include each `market_data_disclosure`
   verbatim and any `order_checks` alerts. Say if the market is closed
   and the orders will queue for the next open.
8. After the user says "yes", place each order with its own fresh UUID
   `ref_id`.
9. Report each order's state, and add an entry to the trade log using
   the robinhood-trade-log skill.

## Example

Holdings VTI $350, VXUS $125, cash $225 (a $200 deposit plus the old $25):

- New total $700. Targets: VTI $490, VXUS $175, cash $35.
- Shortfalls: VTI $140, VXUS $50.
- Money to invest: $225 - $35 = $190.
- Split by shortfall: VTI $140, VXUS $50.
