# moneymaker
Agentic Trading on Robinhood

Use Claude Code with Robinhood's official MCP server to research the market and place real trades through natural language — no code required.

---

## What this is

Moneymaker connects Claude Code to your Robinhood account via the Robinhood MCP server. You can ask Claude to check your portfolio, research a ticker, review news and technicals, size a trade, and place it — all in one conversation. A built-in trading skill enforces a research-before-trading workflow so Claude never jumps straight to placing an order.

You can also use Claude's scheduling feature to set up autonomous routines that run on a cron schedule (e.g., a morning market brief every weekday, or a weekly portfolio rebalance check).

---

## Prerequisites

- **Claude Code** — the CLI or desktop app ([claude.ai/code](https://claude.ai/code))
- **Robinhood account** — with an active login (options trading requires the appropriate account level)
- **Robinhood MCP access** — available through claude.ai integrations

---

## Setup

### 1. Connect the Robinhood MCP server

1. Open Claude Code (CLI or desktop app).
2. Go to **Settings → Integrations** (or run `/config` in the CLI).
3. Find **Robinhood** under available MCP servers and click **Connect**.
4. Log in to your Robinhood account when prompted to authorize the connection.
5. Confirm the connection is active — you should see Robinhood listed under connected integrations.

### 2. Clone this repo (optional but recommended)

```bash
git clone https://github.com/<your-username>/moneymaker.git
cd moneymaker
```

Opening the project in Claude Code loads the trading skill automatically, which tells Claude how to research and structure trades before placing them.

### 3. Start a session

Open Claude Code in the `moneymaker` directory:

```bash
claude
```

You're ready to trade.

---

## Usage

### One-off trading sessions

Just ask Claude what you want:

```
Check my portfolio and give me a summary of my positions.
```

```
Research NVDA and tell me if now is a good time to buy 10 shares.
```

```
Buy 5 shares of AAPL at market price — confirm before submitting.
```

Claude will follow the trading playbook: check accounts and open orders, research news and earnings, check technicals and sentiment, size the trade, review the order, and ask for confirmation before placing it.

### Setting up routines with the schedule skill

You can ask Claude to create scheduled agents that run automatically. Use the `/schedule` skill:

```
/schedule Create a routine that runs every weekday morning at 9am and gives me a market brief before the open — index levels, macro news, and anything notable in my watchlist.
```

```
/schedule Set up a weekly check every Friday at 3pm that reviews my portfolio and flags any positions that are down more than 10% from my cost basis.
```

Claude will set up a cloud agent on a cron schedule using your prompts. You can list, edit, or delete routines at any time.

---

## Agent prompts

These are the prompts currently powering the two agents in this project.

### Trading agent (`.claude/skills/trading/SKILL.md`)

The trading skill is automatically loaded when you open this project in Claude Code. It governs every trade Claude makes:

```
Research a ticker or the market, then place trades via the Robinhood MCP tools.

Steps every time:
1. Establish context — check accounts, portfolio, open positions, open orders, watchlists.
2. Research news — web search for latest news on the ticker and broader market; check earnings
   calendar; note macro catalysts (CPI, FOMC, jobs).
3. Monitor sentiment — read news tone; cross-check against price action using historicals,
   technical indicators, and price book; check fundamentals.
4. Size the trade — confirm buying power and existing exposure; default to conservative sizing;
   check option eligibility if applicable.
5. Review before submitting — call review_equity_order / review_option_order first; state the
   trade in plain language; confirm with the user before placing.
6. After the trade — confirm order status; note the thesis (catalyst + sentiment + technical check).

Guardrails:
- Never place an order without completing the research step in the same session.
- Never increase position size beyond what's needed for the user's stated goal.
- Flag elevated near-term risk (earnings, litigation, macro events) even if not asked.
- Do not chase illiquid options without calling out liquidity risk.
```

### Scheduled market brief agent (example routine prompt)

When you use `/schedule` to create a morning brief routine, Claude generates a cloud agent with a prompt like this:

```
You are a morning market analyst running before the US market open.

1. Fetch current levels for major indexes (S&P 500, Nasdaq, Dow, Russell 2000, VIX).
2. Search for overnight macro news — Fed speakers, international markets, futures, key economic
   releases due today.
3. Pull watchlist items from the Robinhood account and check quotes + any overnight news for each.
4. Check the earnings calendar for today and tomorrow.
5. Summarize in plain language: what happened overnight, what to watch today, and any positions
   that may need attention based on news.

Keep it concise — 5-10 bullet points max. Flag anything that warrants action before the open.
```

---

## Project structure

```
moneymaker/
└── .claude/
    └── skills/
        └── trading/
            └── SKILL.md   # Trading playbook loaded automatically by Claude Code
```
