---
name: aggressive-tech-strategy
description: Daily pre-market aggressive large-cap-tech trading strategy for the agentic Robinhood account. Runs on a schedule, researches and proposes trades, but always confirms with the user before placing any order.
---

# Aggressive Large-Cap Tech Strategy (recommend-and-confirm)

**Account**: Agentic cash account `798768503` (nickname "Agentic") ONLY. Never trade the
individual margin account `805064524` under this strategy.

**Authorization model**: This strategy is aggressive in what it looks for, but NOT autonomous in
execution. Every run must follow the `trading` skill's default guardrail: research, size, and
propose specific trade(s) in plain language (ticker, side, quantity/contracts, order type, est.
cost, chosen stop-loss level) and then message the user to confirm. Never call
`place_equity_order` / `place_option_order` without an explicit go-ahead from the user for that
specific trade. If the user doesn't respond, just leave the proposal in place — do not act.

## Universe

Equities and options only on this fixed list — never propose trades outside it:
AAPL, MSFT, NVDA, GOOGL, AMZN, META, TSLA, AMD, AVGO, NFLX, CRM

## Instruments

- Equities: allowed freely (subject to sizing rules below).
- Options: allowed, but only propose one if the premium for the intended size fits within current
  buying power on the agentic account with room to spare. Check `get_option_chains` /
  `get_option_instruments` and confirm `get_option_level_upgrade_info` allows the strategy before
  proposing. If no name in the universe has an affordable, reasonably liquid contract, stick to
  equities for that day and say so.

## Position sizing (concentrated / aggressive)

- Proposals may use up to 100% of current buying power in 1-2 highest-conviction names at a time.
- "Conviction" must be justified by the research steps below (news catalyst + sentiment +
  technical confirmation) — never size up on a coin-flip signal.
- Prefer concentration over spreading thin across the whole universe.

## Pre-market research process (every run)

Follow the `trading` skill's full process:
1. Check accounts/positions/orders/watchlists for account `798768503` (and note buying power —
   if $0, report that and stop; do not propose anything).
2. Research overnight/pre-market news + the macro calendar (Fed/CPI/jobs, earnings this week) for
   candidate names in the universe.
3. Cross-check sentiment against pre-market price action and technicals.
4. Sanity-check fundamentals for anything you're about to size heavily into.
5. If any existing position is already past its stop-loss level, flag closing it as the top
   priority proposal before any new entry.
6. Draft the specific proposed trade(s), including a chosen stop-loss (20-25% band) for any new
   position, and present them to the user for confirmation.

## Exit rule (hard stop-loss)

- Any open position should be flagged for closing if it has dropped 20-25% from entry —pick a
  specific number in that band based on the position's volatility. This is a proposal like any
  other; still needs confirmation before the agent closes it, unless the user has separately
  pre-authorized stop-loss exits specifically.

## Frequency

Runs once per trading day, 30 minutes before market open, so proposals are ready to act on (or
skip) right at the open.

## Guardrails

- Never propose a name outside the fixed universe.
- Never touch the non-agentic account.
- Never propose sizing beyond available buying power.
- If the agentic account has $0 / no buying power, report that plainly and stop.
- Always surface elevated near-term risk (earnings, litigation, regulatory action) on any
  candidate name, even though the mandate is aggressive.
- Flag liquidity risk (wide spreads) on any options proposal.
- Never place an order without the user's explicit confirmation of that specific trade.
