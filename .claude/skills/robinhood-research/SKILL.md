---
name: robinhood-research
description: Research a stock or ETF on a fixed checklist using the Robinhood-Trading tools. Use when the user asks about a specific ticker, whether to buy something, or how two investments compare. Research only; never places orders.
---

# Research brief

This is research, not a trade. Never place orders from this skill. If the
user then wants to buy, the robinhood-guardrails skill applies; anything
outside VTI, VXUS, BTC and ETH needs the user's explicit approval by
name.

For crypto, most of the stock checklist does not apply. Use
`get_crypto_quotes` (mark price, spread, change vs. `open_price`) and
recent news, and be direct about the risks: very large price swings,
no earnings or dividends, and regulatory uncertainty.

## Checklist

Call these for the symbol (use `search` first if the name is unclear):

1. `get_equity_quotes`: current price, today's change.
2. `get_equity_fundamentals`: market cap, P/E, dividend yield,
   52-week range, and for ETFs the expense ratio if available.
3. `get_equity_historicals`: 1-year and 5-year price change.
4. `get_equity_analyst_ratings` (stocks only): buy/hold/sell counts and
   price targets.
5. `get_equity_news`: the 3 to 5 most relevant recent headlines.
6. `get_earnings_calendar` (stocks only): next earnings date.
7. `get_equity_positions` for the Agentic account: whether the user
   already owns it, or owns a fund that includes it.

## Brief format

- **What it is:** one or two plain sentences.
- **Numbers:** a small table of price, 1y and 5y change, P/E or expense
  ratio, dividend yield.
- **What analysts and news say:** a few bullets, with dates.
- **Risks:** the two or three that matter most.
- **Fit with your portfolio:** overlap with what the user already owns
  (for example, VTI already holds every large US company), and how it
  compares with the 65/20/7/3/5 plan (VTI/VXUS/BTC/ETH/cash).
- **Bottom line:** a balanced summary. State that this is information,
  not a guarantee. Never predict a price.

## Comparing two investments

Use the same checklist for both and put them side by side in one table,
then explain the main difference in plain language.
