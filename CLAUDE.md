# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**VortexBot** - Algorithmic mean-reversion trading bot with AI decision layer. Instrument-agnostic, config-driven, **hybrid Python/Node.js architecture**.

**Philosophy**: Conservative, disciplined, data-driven. No hype. Prove or disprove the edge exists—both outcomes are valuable.

---

## Project Phases & Current Status

### Phase 1 – Hardware Audit & Model Setup (Weeks 1-2)

- [x] Confirm hardware (Ryzen 7 5800X, 64GB RAM, RX 6800 XT 16GB VRAM, Windows 11)
- [x] Install and benchmark local LLM (Qwen 2.5 Coder 7B via Ollama, ~1s latency)
- [x] Connect to Deriv demo API and stream VIX 75 ticks (`ticks_rsi_v75.js`)
- [ ] Build historical backtester (Python) for VIX 75 mean-reversion
- [ ] Generate initial backtest report (win%, profit factor, max drawdown)

### Phase 2 – VortexBot MVP & Backtesting (Weeks 3-8)

- [ ] Implement full backtesting engine with config-driven strategies
- [ ] Integrate LLM decision layer into backtesting loop
- [ ] Build demo bot (Node.js) trading on Deriv demo with journal logging
- [ ] Achieve target metrics (55%+ win rate, PF ≥ 1.5, max DD < 15%)

### Phase 3 – Real Capital Testing (Months 4-15)

- [ ] Start with $500 real capital (1% risk/trade)
- [ ] Run for 6–12 months
- [ ] Monthly P&L reviews and "continue or stop" decisions

---

## Canonical Config Files

These are the authoritative files for core system behavior:

| File | Purpose |
|------|---------|
| `config/v75-mean-reversion.json` | VIX 75 strategy parameters (RSI period, BB settings, TP/SL, risk %). Read by both Node.js and Python. |
| `node/core/risk-engine.js` | Single source of truth for risk constraints (1% max risk, position sizing, exposure checks). |
| `python/backtest/run_backtest.py` | Main backtester entrypoint; reads configs from `config/` and writes results to `logs/` and `journal/`. |
| `.env.example` | Template for API keys (Deriv API token, app ID) and environment variables. |

---

## Hybrid Architecture

This project uses **two languages for different purposes**:

| Component | Language | Purpose |
|-----------|----------|---------|
| **Live Trading** | Node.js | Deriv WebSocket, real-time streaming, order execution, risk enforcement |
| **Backtesting & Research** | Python | Historical data analysis, backtesting, ML models, strategy optimization |

**Why this split?**
- Python has mature backtesting libraries (Backtrader, VectorBT, Pandas, NumPy) for research
- Node.js excels at async I/O and real-time WebSocket connections for live trading

---

## Common Development Commands

### Node.js (Live Trading)

```bash
# Install Node dependencies
npm install

# Run existing tick stream test (requires .env with DERIV_API_TOKEN)
node ticks_rsi_v75.js
```

### Python (Backtesting & Research)

```bash
# Install Python dependencies
pip install -r python/requirements.txt

# Run backtest (to be built)
python python/backtest/run_backtest.py --config config/v75-mean-reversion.json
```

### Environment Setup

```bash
# Create .env from example
cp .env.example .env
# Then edit .env with your Deriv API credentials
```

---

## Architecture & Code Organization

### Project Structure

```
vortexbot/
├── node/                    # Node.js live trading
│   ├── config/              # Strategy and instrument configurations
│   ├── instruments/         # Deriv symbol mappings
│   ├── strategies/          # Reusable strategy logic
│   ├── core/                # Risk-engine, trade-logger, execution
│   └── models/              # LLM integration (Ollama)
├── python/                  # Python backtesting & research
│   ├── backtest/            # Backtesting engine
│   ├── strategies/          # Strategy implementations (Python versions)
│   ├── models/              # ML models (scikit-learn, XGBoost, etc.)
│   └── notebooks/           # Jupyter notebooks for analysis
├── config/                  # Shared strategy configs (JSON)
├── data/                    # Shared historical and tick data (CSV/Parquet)
├── logs/                    # Shared trade logs and backtest results
└── journal/                 # SQLite trade journal database
```

### Data Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                         LIVE TRADING (Node.js)                  │
├─────────────────────────────────────────────────────────────────┤
│ Deriv WebSocket → Tick Data → Strategy → Risk Engine → Execute  │
└─────────────────────────────────────────────────────────────────┘
                                    ↓
┌─────────────────────────────────────────────────────────────────┐
│                      RESEARCH (Python)                          │
├─────────────────────────────────────────────────────────────────┤
│ Historical Data → Backtest → Optimize → ML Models → Validate    │
└─────────────────────────────────────────────────────────────────┘
```

**Node.js and Python share:**
- `data/` folder (CSV/Parquet historical data)
- `config/` folder (JSON configs readable by both)
- `journal/` database (SQLite)

---

## Critical Constraint: All Trades Must Pass Through Risk Engine

The `node/core/risk-engine.js` is the **single point of validation** for every trade. It enforces:
- **1% max risk per trade** (non-negotiable)
- Position size limits
- Exposure checks (max concurrent positions)

**Never bypass or duplicate risk logic**—all trade paths must route through `node/core/risk-engine.js`.

---

## Non-Negotiable Code Constraints

1. **No hardcoded trading symbols** - All symbols and instrument parameters must come from `config/` or `instruments/`
2. **Everything gets logged** - Wins, losses, errors, decisions, LLM reasoning
3. **Never silently swallow errors** - Graceful failure with logging required
4. **Code must work offline** (backtest mode) - Don't assume live WebSocket connection
5. **Risk > 1% is prohibited** - Any code allowing >1% risk is a bug
6. **Use the right tool**: Live trading = Node.js, Backtesting/ML = Python

---

## When Adding Features

Ask yourself:
- **Is this live trading or research?** → Node.js vs Python
- Is this instrument-agnostic?
- Does `node/core/risk-engine.js` validate this?
- Is it logged?
- Can it be configured via `config/`?
- Does it work offline (backtest mode)?

---

## Prohibited Patterns

- ❌ Hardcoded symbols like `R_75` scattered in code
- ❌ Risk percentage calculations not validated by `risk-engine.js`
- ❌ Silent `catch` blocks without logging
- ❌ Trading logic without backtest coverage
- ❌ Quick-money mindset (e.g., "increase risk to recover losses")
- ❌ Writing backtests in Node.js when Python is more suitable

---

## Tech Stack Notes

### Node.js
- **technicalindicators** - RSI, Bollinger Bands, etc. (already installed)
- **Deriv WebSocket API** - See `ticks_rsi_v75.js` for connection pattern
- **ws** - WebSocket client (already installed)
- **dotenv** - Environment configuration (already installed)

### Python
- **Backtrader** / **VectorBT** - Backtesting frameworks
- **Pandas** / **NumPy** - Time-series analysis and numeric computing
- **scikit-learn** / **XGBoost** - ML models for ensemble strategies
- **Jupyter** - Research notebooks

### LLM (Shared)
- **Ollama** (Qwen 2.5 Coder 7B / Phi-3 Mini) - Local inference via DirectML
- **DirectML** - AMD GPU inference path (ROCm not viable on RX 6800 XT)

---

## Maintenance Rule

**When updating this project:**
- Whenever new folders, core files, or project phase changes are introduced, update the "Project Phases & Current Status" and "Canonical Config Files" sections above.
- Keep this file as the primary human-readable overview of the system.
- This file serves as context for both Claude Code and the Perplexity Space.

---

## When in Doubt

Conservative over aggressive. Data over opinion. Discipline over hype.
