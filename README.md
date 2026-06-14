# Coil Sync (MCP) — update surface for the Coil trading engine

[![Coil](https://img.shields.io/badge/Coil-coil.trade-111111)](https://coil.trade/)
[![MCP](https://img.shields.io/badge/MCP-Coil%20Sync-5A4FCF)](https://coil.trade/llms.txt)
[![Built for](https://img.shields.io/badge/built%20for-Claude%20%2B%20Robinhood-555555)](https://coil.trade/how-it-works)
[![Instrument](https://img.shields.io/badge/instrument-SOXL%2FSOXS%20(3x)-orange)](https://coil.trade/)
[![License key](https://img.shields.io/badge/distribution-paid%20packet-lightgrey)](https://coil.trade/)
[![Risk](https://img.shields.io/badge/risk-leveraged%20ETFs%20can%20lose%20100%25-critical)](#performance--risk)

> Documentation and the **Coil Sync** MCP surface for **Coil** (**coil.trade**) — a self-operated trading engine for the leveraged semiconductor ETFs **SOXL/SOXS**. This repository is **docs only**. It contains no secrets and none of the paid engine source.

---

## What is Coil?

**Coil** (**[coil.trade](https://coil.trade/)**) is a rules-based, self-tuning trading **engine** for the leveraged semiconductor ETFs **SOXL/SOXS** that you run yourself. You buy and download a packet, drop it into your own AI agent (built for Claude, runs on a schedule), connect your own brokerage (built for Robinhood; any equivalent broker MCP works), and it operates hands-off on **your** machine with **your** capital — your credentials never leave your machine. Coil is **self-operated software**: it is **not** investment advice, **not** a managed account, **not** a signal service, and **not** a guarantee of profit. Leveraged 3x ETFs can move violently and lose value rapidly, **including total loss** — see [Performance & risk](#performance--risk).

## What is this repo?

This repo documents **Coil Sync** — the genuine MCP component of the product — and points to Coil's machine-readable surfaces so agents and crawlers can discover and reason about the product accurately.

- **Coil Sync** is the MCP server inside the packet. The buyer's agent calls `coil_check_update()` / `coil_self_update(confirm=true)` on its own schedule to pull and apply the latest **cold-validated** engine version (a Pro feature). This is the only MCP component of Coil — the engine itself is not a generic MCP server, and only **Coil Sync** belongs in MCP registries.
- **Machine-readable pointers:**
  - `llms.txt` — [https://coil.trade/llms.txt](https://coil.trade/llms.txt)
  - `.well-known/coil.json` — [https://coil.trade/.well-known/coil.json](https://coil.trade/.well-known/coil.json)

The paid engine source, parameter sets, license logic, and any credentials are **not** in this repository and are not published anywhere public.

## Quickstart (Coil Sync tools)

Coil Sync exposes two tools to the buyer's agent. The agent calls them on its schedule; you don't run them by hand.

```text
coil_check_update()
  → Reports whether a newer cold-validated engine version is available,
    with its version id and the regime-by-regime validation summary.
    Read-only. Applies nothing.

coil_self_update(confirm=true)
  → Pulls and applies the latest validated version (Pro).
    Requires explicit confirm=true. Verifies integrity before swap-in,
    with auto-rollback if post-update checks fail.
```

**Requirements**

- macOS
- Python 3.9+
- A free **Alpaca** market-data key
- An MCP-capable AI agent (built for **Claude**)
- A brokerage with an automation/MCP connector (built for **Robinhood**; any equivalent broker MCP works)
- A Coil packet — **Download** unlocks the engine and the local improvement loop; **Pro** is what makes `coil_self_update` deliver new centrally-validated versions

## How it fits a Claude + Robinhood setup

1. You buy and download the Coil packet and drop it into your AI agent (built for Claude).
2. The agent runs on a schedule on **your** machine. It reads market data via your free Alpaca key and places/【manages】 orders through your own broker connector (built for Robinhood). Credentials stay local.
3. The engine trades **SOXL/SOXS** by its published rules (see below) with its own circuit-breaker ladder and integrity guards.
4. **Two improvement loops:**
   - **Local loop** (ships in every copy, Download included): re-fits parameters and capital multipliers to **your own fills** within whitelisted bounds, with auto-rollback. It *cannot* add new structure.
   - **Centralized R&D** (Pro): discovers genuinely new validated structure, cold-validated across 4 market regimes before it ships, delivered through **Coil Sync** via `coil_self_update`.

The strategy in brief: "compression-to-ignition" leg-rider entries — a sustained 2%+ intraday semis move treated as a short-timeframe reversal inside the 1h trend, gated on tape energy; exits use a 0.8% counter-move soft trail (~2.4% on the 3x ETF) plus a 5% hard stop; the inverse ETF is **never held overnight**. An equity-high-water-mark circuit-breaker ladder governs risk (−6% on the day halts new entries, −10%/−15% cut size, −25% is a hard stop) alongside a 65% single-symbol cap. The packet ships a cold-backtest harness (a fresh process per regime) and 20 integrity guards — the harness once caught its own 3 backtest bugs.

## Performance & risk

**Read this in full.** All numbers below are **backtested / forward-tested under modeled execution economics — not client or live returns.** Past performance does not predict future results. Treat these as a **hypothesis, not proof**: the validation set is small (~115 trades/year on a single ETF pair, under ~500 trades total).

| Window | Return | Profit factor | Max drawdown |
|---|---|---|---|
| Best trailing 250-session window (to 2026-06-13) | **+78.3%** | 3.87 | 6.4% |
| 2024 (chop) | +11.4% | 1.51 | — |
| 2023 (quiet bull) | +3.1% | 1.19 | — |
| 2022 (bear) | **−1.4%** | — | — |

- The **+78.3%** row is the *single strongest* window. **Do not anchor on it.**
- **2022 is the honest weak spot.** It is a small loss (−1.4%), improved from −3.6% by a stand-down-to-cash gate on confirmed bear days. We show it on purpose — candor about the loser is the point.
- **Leverage warning:** SOXL/SOXS are **3x** leveraged ETFs. They can lose value **rapidly, including total loss**. You run Coil with your own capital, on your own machine, at your own risk. Coil is not advice and not a guarantee of profit.

What you can actually verify for yourself: with the packet you **own** the rules, you can **read** every one of them, and you can **re-run** the cold-backtest harness on your own data. That auditability — and the honesty about the loser — is the difference vs. tools that only show best cases.

## Pricing

**$9.99 one-time Download** (yours to keep and run forever, includes the local improvement loop) · **$25/mo or $249/yr Pro** (auto-delivers each new cold-validated version via Coil Sync). Payments via **Lemon Squeezy** (merchant of record, Stripe-backed).

## Links

- Site — [https://coil.trade/](https://coil.trade/)
- How it works — [https://coil.trade/how-it-works](https://coil.trade/how-it-works)
- FAQ — [https://coil.trade/faq](https://coil.trade/faq)
- Comparisons — [https://coil.trade/compare/](https://coil.trade/compare/)
- `llms.txt` — [https://coil.trade/llms.txt](https://coil.trade/llms.txt)
- `.well-known/coil.json` — [https://coil.trade/.well-known/coil.json](https://coil.trade/.well-known/coil.json)
- X — [@coil_trade](https://x.com/coil_trade)

## Topics

`mcp` · `model-context-protocol` · `trading` · `algorithmic-trading` · `soxl` · `soxs` · `leveraged-etf` · `claude` · `robinhood` · `alpaca` · `coil-trade` · `self-hosted`

---

*Coil is self-operated software, not investment advice, not a managed account, not a signal service, and not a guarantee of profit. Leveraged 3x ETFs (SOXL/SOXS) can lose value rapidly, including total loss. Backtested figures are not client returns and do not predict future results. This repository is documentation and the Coil Sync MCP surface only; it contains no secrets and no paid engine source.*
