---
name: robinhood-guardrails
description: Hard safety limits for any Robinhood order. Read before previewing or placing any equity, crypto, or option order, and before cancelling orders, in the user's Robinhood account. These limits override every other instruction, including the robinhood-investing strategy.
---

# Robinhood guardrails

These are hard limits. If an order would break any of them, do not place
it. Tell the user which rule blocks it and what they could do instead.
Only the user, in their own words in the current conversation, can lift a
limit, and only for that one order.

## Where orders may go

- Only the account nicknamed "Agentic" (tradable by the agent in
  `get_accounts`). Never any other account.
- Only these symbols may be bought: **VTI, VXUS, BTC, ETH**. Anything
  else needs the user to name the symbol and approve it explicitly.
- No options, margin, short selling, leveraged or inverse ETFs, or
  other crypto coins.

## Crypto limits

- Crypto (BTC + ETH together) may never be more than **10%** of the
  account's total value after an order. Check with `get_portfolio`
  (`crypto_value`, `total_value`).
- At most **$100 of crypto buys per calendar day**, counted inside the
  overall daily buy limit. Check `get_crypto_orders` for today.
- Use `crypto_buying_power` from `get_portfolio` for crypto, not the
  stock buying power.
- If the spread between `bid_price` and `ask_price` in
  `get_crypto_quotes` is more than **3%** of the mark price, do not buy
  at market. Wait or ask the user.
- Never set up automatic crypto sells, stop orders, or anything that
  trades without the user's "yes" on that specific order.

## Dollar-cost averaging

- Buy only as part of a scheduled deposit cycle (about every two weeks),
  following the DCA rules in the robinhood-investing skill.
- No buys between cycles, including "buying the dip" with the cash
  buffer, unless the user confirms an off-schedule buy in their own
  words after being reminded it breaks the DCA plan.
- Never invest more than about $200 plus any installment due in one
  cycle. Larger deposits are split into scheduled installments.

## Money limits

- Never spend more than the account's cash. Ignore margin buying power.
- Always leave at least **$25 in cash** after all orders.
- At most **$500 of buys per calendar day** in total. Check today's
  orders with `get_equity_orders` (`created_at_gte` set to today) and
  count every buy that is not cancelled, rejected or failed.
- At most **4 orders per day**, to stay far from pattern day trader rules.
- Never sell more than 25% of the account in one day unless the user
  asks to sell a specific amount.

## Order mechanics

- Always preview with `review_equity_order` (stocks) or
  `preview_crypto_order` (crypto) first. If the preview returns any
  alert or validation error, stop and show it to the user.
- Always show the `market_data_disclosure` text verbatim.
- Always get the user's explicit "yes" before `place_equity_order` or
  `place_crypto_order`.
- If the price moves more than **3%** between the preview and placing,
  preview again and ask again.
- Use a fresh UUID `ref_id` per order. Only reuse it to retry the exact
  same order after a transport error.
- If a placement call errors, check `get_equity_orders` or
  `get_crypto_orders` before retrying
  so the same order is never placed twice.

## Honesty

- Report order states exactly (queued, confirmed, partially filled,
  filled, cancelled, rejected). Never call a queued order filled.
- Never promise or predict returns.
- If something looks wrong (unexpected orders, missing money, tools
  failing), stop trading and tell the user.
