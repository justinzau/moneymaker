---
name: trading
description: Basic playbook for agentic trading on Robinhood — research a ticker or the market, then place trades via the robinhood-trading MCP tools. Use when asked to trade, rebalance, check the market, or evaluate a position.
---

# Trading

This skill governs how to research and execute trades through the `robinhood-trading` MCP
connection. Follow this process every time before placing an order. Never skip straight to
`place_equity_order` / `place_option_order` without doing the research steps first.

## 1. Establish context before acting

- Check current positions and buying power: `get_accounts`, `get_portfolio`,
  `get_equity_positions`, `get_option_positions`.
- Check open orders so you don't duplicate or conflict with something already working:
  `get_equity_orders`, `get_option_orders`.
- Note any existing watchlists relevant to the request: `get_watchlists`, `get_watchlist_items`.

## 2. Research the news and events

- Use `WebSearch` / `WebFetch` to pull the latest news for the ticker(s) in question, and for the
  broader market (index moves, Fed/macro events, sector news).
- Check `get_earnings_calendar` and `get_earnings_results` — avoid opening new directional
  positions right before an earnings print unless that's explicitly the strategy.
- Look for scheduled macro catalysts (CPI, FOMC, jobs report) in the same window as the trade.
- Summarize what you found in 2-4 bullet points before moving on: what's the news, is it priced in,
  what's the surprise risk.

## 3. Monitor sentiment

- Read news tone (bullish/bearish framing, analyst upgrades/downgrades) from the search results.
- Cross-check price action against sentiment: `get_equity_historicals`,
  `get_equity_technical_indicators`, `get_equity_price_book` — does price action confirm or
  contradict the narrative?
- Check fundamentals for a sanity check on valuation: `get_equity_fundamentals`, `get_financials`.
- If sentiment and price action strongly disagree, flag this explicitly rather than picking a side
  silently.

## 4. Size and structure the trade

- Confirm buying power and existing exposure to this ticker/sector before adding more.
- Default to conservative position sizing — no single new position should be a large fraction of
  the account without the user explicitly asking for concentrated risk.
- For options, check `get_option_chains` / `get_option_instruments` /
  `get_option_level_upgrade_info` to confirm the account is even eligible for the requested
  strategy.

## 5. Review before submitting

- Always call `review_equity_order` / `review_option_order` first and read back the resulting
  price, fees, and estimated cost/proceeds.
- State the trade in plain language (ticker, side, quantity, order type, limit price, est. cost)
  before calling `place_equity_order` / `place_option_order`.
- Treat placing an order as an irreversible, real-money action: confirm with the user first unless
  they have explicitly pre-authorized autonomous trading for this session.

## 6. After the trade

- Confirm the order status with `get_equity_orders` / `get_option_orders`.
- Note the thesis (news catalyst + sentiment read + technical/fundamental check) somewhere visible
  in the response so the reasoning is auditable later — don't just report "bought X shares."

## Guardrails

- Never place an order without having done the news + sentiment research step in the same session.
- Never increase position size beyond what's needed to fulfill the user's stated goal.
- If news/events suggest elevated near-term risk (pending earnings, litigation, regulatory action),
  say so even if the user didn't ask.
- Do not chase illiquid options or thinly traded names without pointing out the liquidity risk
  (wide spreads, slippage) using `get_equity_price_book` / option quotes.
