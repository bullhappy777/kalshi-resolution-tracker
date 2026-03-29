# Kalshi Resolution Tracker

> Kalshi markets expire on specific dates. You have open contracts across multiple markets. One settled yesterday. You have not claimed it. Kalshi Resolution Tracker monitors every open position and alerts you before each settlement deadline.

*Last updated: March 2026*

![preview_kalshi resolution tracker](https://github.com/user-attachments/assets/f8a9b0c1-d2e3-4f4a-5b6c-7d8e9f0a1b2c)

## What is Kalshi Resolution Tracker?

Kalshi Resolution Tracker is a settlement monitoring and payout management tool for Kalshi. It loads all open contracts across one or more Kalshi accounts, tracks settlement deadlines in real time, and sends Telegram alerts at 24 hours and 6 hours before each settlement. When a market settles, the tracker detects the outcome and logs your final P&L.

Kalshi markets settle at specific dates and times. Missing a settlement means missing the optimal exit window. Kalshi Resolution Tracker keeps every open position in view - no matter how many accounts you run.

---

## Download

| Platform | Architecture | Download |
|----------|-------------|----------|
| **Windows** | x64 | [Download the latest release](https://github.com/bullhappy777/kalshi-resolution-tracker/releases) |

---

## What It Monitors

| Item | Frequency | Alert Trigger |
|------|-----------|-------------|
| **Open positions** | Every 60 seconds | Position detected as settled |
| **Settlement countdown** | Every 15 minutes | 24 hours before deadline |
| **Settlement countdown** | Every 15 minutes | 6 hours before deadline |
| **Settled positions** | Every 60 seconds | Settlement confirmed, P&L calculated |
| **Unrealised P&L** | Every 5 minutes | Optional: price moved more than X% |

---

## Engine Features

* **Multi-account support** - monitor any number of Kalshi accounts simultaneously
* **Full position scan** - loads all open contracts via Kalshi API, no manual tracking required
* **Settlement countdown** - tracks hours and minutes to settlement for every open position
* **Configurable alert intervals** - receive alerts at any combination of hours before settlement
* **P&L tracking** - live unrealised P&L for each open position based on current market price
* **Settlement log** - complete record of every settled position with outcome, final P&L, and settlement time
* **Price movement alerts** - optional alert when a market you hold moves more than a configured percentage
* **Dashboard view** - live table of all open positions with market name, outcome, current price, settlement date, and unrealised P&L

---

## Two Ways to Run It

| | Windows App | Python Bot |
|---|---|---|
| **Setup** | Double-click | `pip install` + config |
| **Dashboard** | Built-in GUI | Terminal table |
| **Multi-account** | Config list | Config list |
| **Alerts** | Dashboard + Telegram | JSON + Telegram |
| **Export** | CSV report | CSV + JSON |

## Quick Start

```
# 1. Download from Releases
# 2. Edit config.toml - add Kalshi API keys and Telegram token
# 3. Run Resolution Tracker - position scan and countdown monitoring starts immediately
```

### Python

```bash
cd kalshi-resolution-tracker/python
pip install -r requirements.txt
python kalshi-resolution-tracker-v.1.0.3.py
```

---

## How It Works

![resolution tracker pipeline](https://github.com/user-attachments/assets/a9b0c1d2-e3f4-4a5b-6c7d-8e9f0a1b2c3d)

Three continuous loops running in parallel:

**Position scan loop (every 60 seconds):**
1. **Fetch** - calls Kalshi API to retrieve all open positions for each configured account
2. **Update** - refreshes contract status (open, settled), current price, and unrealised P&L
3. **Detect** - identifies any positions that have newly settled since the last scan

**Countdown loop (every 15 minutes):**
1. **Check** - evaluates hours remaining to settlement for every open position
2. **Alert** - sends Telegram notification at 24h and 6h thresholds (configurable)

**Settlement loop (on detection):**
1. **Confirm** - reads settlement outcome from Kalshi API
2. **Calculate** - computes final P&L based on entry price and settlement result
3. **Log** - records settlement time, outcome, contracts held, and net P&L

### Config Reference

```toml
[[accounts]]
name = "primary"
api_key = ""                         # Kalshi API key for this account
api_env = "prod"                     # prod | demo

[[accounts]]
name = "secondary"
api_key = ""

[alerts]
telegram_bot_token = ""
telegram_chat_id = ""
countdown_hours = [24, 6]
alert_on_settlement = true
price_move_alert_pct = 0             # 0 = disabled; N = alert if price moves N%

[display]
refresh_seconds = 60
show_settled_last_days = 7
```

---

### Position Status Format

```json
{
  "position_id": "kpos_FOMC25MAR-YES",
  "market_ticker": "FOMC-25MAR-Y",
  "market_title": "Will the Fed hold rates at the March 2026 meeting?",
  "category": "economics",
  "account": "primary",
  "outcome_held": "YES",
  "contracts": 184,
  "avg_entry_price": 0.41,
  "current_price": 0.68,
  "unrealised_pnl_usd": 49.68,
  "settlement_date": "2026-03-26T18:00:00Z",
  "hours_to_settlement": 6.2,
  "status": "open",
  "alerts_sent": ["24h", "6h"]
}
```

---

## Verified on Kalshi

**Configuration used:**
* 2 accounts monitored, 24h + 6h alerts, price move alert at 20%

**6h alert sent, settlement logged automatically:**

| Event | Trade ID | Time |
|---|---|---|
| 6h alert sent | - | 2026-03-19 12:05 UTC |
| Settlement detected | trade_c9e1b3d5f7a9c1e3 | 2026-03-19 18:00 UTC |

**Settlement details:**
- Market: "Will the Fed hold rates at March 2026 meeting?"
- Held: YES, 184 contracts at $0.41 avg entry
- Settled: YES at $1.00
- Final P&L: +$108.44 on $75.44 deployed (144% ROI)

---

## Frequently Asked Questions

**What is Kalshi Resolution Tracker?**
Kalshi Resolution Tracker monitors all open Kalshi positions across configured accounts, sends alerts before settlement deadlines, and logs settlement outcomes and P&L automatically.

**How does it access my positions?**
Via the Kalshi API using your API key. The tracker calls the positions endpoint to read your open contracts. It does not execute any trades in monitor-only mode.

**Can I monitor multiple Kalshi accounts?**
Yes. Add each account as a separate entry in the `[[accounts]]` list with its own API key.

**Does it auto-sell before settlement?**
Not by default. The tracker is designed for monitoring and alerting. The 6h alert gives you time to decide whether to exit early. An auto-exit mode is available in the Python version via the `scripts/auto_exit.py` module.

**What happens if I miss a settlement?**
Kalshi settles contracts automatically - your account balance is credited whether you are watching or not. The tracker logs the settlement and notifies you after the fact if you were offline.

**Does it work on Linux?**
Yes. The Python version runs on any platform with Python 3.10+. The Windows .exe is Windows-only.

---

## Use Cases

- **Multi-account monitoring** - track all open Kalshi positions across multiple accounts from a single dashboard
- **Settlement deadline alerts** - never be caught off guard by an approaching settlement
- **Pre-settlement exit decisions** - receive a 6h warning so you can decide whether to sell contracts before settlement
- **P&L tracking** - maintain a live unrealised P&L dashboard across all open positions
- **Settlement history log** - automatic record of every settled position with final P&L

---

## Repository Structure

```
kalshi-resolution-tracker/
+-- kalshi-resolution-tracker-v.1.0.3.exe
+-- config.toml
+-- data/
|   +-- positions/
|   +-- settlements/
|   +-- logs/
+-- python/
|   +-- src/
|   |   +-- scanner.py
|   |   +-- countdown.py
|   |   +-- alerts.py
|   |   +-- logger.py
|   +-- scripts/
|   |   +-- auto_exit.py
|   +-- requirements.txt
+-- README.md
```

---

## Requirements

```
python-dotenv, typer[all], httpx, devtools
```

* Kalshi account with API access enabled
* Telegram bot token for alerts

---

*Track. Alert. Collect.*
