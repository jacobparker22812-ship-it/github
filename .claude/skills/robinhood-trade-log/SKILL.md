---
name: robinhood-trade-log
description: Keep the user's investing history in investing/trade-log.md in the repo. Use after any Robinhood order is placed, fills, or is cancelled, after a deposit is noticed, or when the user asks for their trade history, cost basis, or records for taxes.
---

# Trade log

The log lives at `investing/trade-log.md` in this repo. It is the user's
permanent record, so keep it accurate and never delete old rows.

## What to record

- **Deposits:** date and amount, from `get_portfolio` or what the user
  says.
- **Orders:** add a row when an order is placed. Update the same row
  when it fills or is cancelled, using `get_equity_orders` or
  `get_crypto_orders` with the order id for the fill price and
  quantity. For crypto, write the quantity as coins (for example
  `0.000580 BTC`) and include any fee.

## Rules

- Mask account numbers (last 4 digits only). Never write full account
  numbers, order ids beyond their first 8 characters, or any personal
  details.
- Dates are in Eastern time, YYYY-MM-DD.
- Show amounts to the cent and shares to 6 decimal places, as Robinhood
  reports them.
- Keep the "Reason" short: for example "Initial investment", "Deposit
  split", "Rebalance".
- Update the Totals section after each change.
- Commit with a message like `Log VTI buy 2026-09-24` and push to the
  current working branch.

## Answering questions

Use the log for history, total deposited and cost basis. For current
value, always fetch live data; the log is not a price source. For tax
questions, point out that Robinhood's official 1099 is the record that
counts. Crypto is taxed as property, so every crypto sale is a taxable
event; mention this if the user asks about selling crypto.
