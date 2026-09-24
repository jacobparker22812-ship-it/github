---
name: robinhood-deposit
description: Invest new money that has arrived in the user's Robinhood Agentic account. Use when the user says they added or deposited money, asks to invest their cash, or a check-in finds cash above the buffer.
---

# Deposit handler

Follow the robinhood-guardrails skill for every order. The targets come
from the robinhood-investing skill: VTI 65%, VXUS 20%, BTC 7%, ETH 3%,
cash 5%.

Every buy follows the dollar-cost averaging rules in the
robinhood-investing skill: invest scheduled deposits (about $200 every
two weeks) on arrival, whatever the price, with no extra buys in between.

## Steps

0. Decide how much of the cash this cycle may invest:
   - A normal deposit (up to $400): invest all of it this cycle.
   - A lump sum (over $400): invest about $200 this cycle, and record
     the remaining installments in `investing/trade-log.md` under
     "Scheduled installments", one per future two-week cycle.
   - Also include any installment due this cycle from an earlier lump
     sum.
   - Money not due this cycle stays in cash and is not counted as
     "money to invest" in step 4.
1. `get_accounts`: find the account nicknamed "Agentic".
2. `get_portfolio`: note cash, buying power and pending deposits.
   Use buying power (not margin) as the spendable amount.
3. `get_equity_positions` and `get_equity_quotes` for the funds, and
   `get_crypto_positions` (with `rhs_account_number`) and
   `get_crypto_quotes` (use `mark_price`) for BTC and ETH: current
   value of each holding.
4. Work out the split:
   - New total = current holdings value + cash.
   - Target value for each fund = new total x its target %.
   - Shortfall for each fund = target value - current value (never below 0).
   - Money to invest = the amount due this cycle from step 0, but never
     more than cash - max($25, 5% of new total).
   - Spread the money to invest across the funds in proportion to
     their shortfalls. This rebalances without selling.
   - Skip any buy under $1 (Robinhood's minimum for dollar orders) and
     leave that money in cash.
5. Check the guardrails: daily buy limit, $100 daily crypto limit,
   10% crypto cap, crypto spread, cash floor, allowed symbols. If the
   crypto limit cuts a crypto buy, leave the difference in cash for the
   next deposit rather than moving it into the funds.
6. Preview each fund buy with `review_equity_order` as a market order
   with `dollar_amount` and `market_hours: regular_hours`. Preview each
   crypto buy with `preview_crypto_order` as a market order with
   `dollar_amount` and the `rhs_account_number`.
7. Show the user one table: holding, amount, estimated shares (funds)
   or coins (crypto), and the resulting mix vs. targets. Include each
   `market_data_disclosure` verbatim, any alerts, and the crypto
   spread. Say if the stock market is closed and fund orders will queue
   for the next open; crypto fills right away at any hour.
8. After the user says "yes", place each order with its own fresh UUID
   `ref_id`.
9. Report each order's state, and add an entry to the trade log using
   the robinhood-trade-log skill.

## Example

Holdings VTI $350, VXUS $125, no crypto, cash $225 (a $200 deposit plus
the old $25):

- New total $700. Targets: VTI $455, VXUS $140, BTC $49, ETH $21,
  cash $35.
- Shortfalls: VTI $105, VXUS $15, BTC $49, ETH $21 (total $190).
- Money to invest: $225 - $35 = $190.
- Split by shortfall: VTI $105, VXUS $15, BTC $49, ETH $21.
- Crypto total $70, within the $100 daily limit and the 10% cap.
