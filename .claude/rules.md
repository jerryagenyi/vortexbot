# Claude Code Rules - VortexBot

## Project Overview

**Project**: VortexBot - Algorithmic mean-reversion trading bot with AI decision layer
**Current Focus**: VIX 75 (Deriv) mean-reversion strategy
**Architecture**: Instrument-agnostic, config-driven, **hybrid Python/Node.js**
**Philosophy**: Conservative, disciplined, data-driven. No hype.

---

## Hybrid Architecture

**Node.js** → Live trading (Deriv WebSocket, real-time streaming, order execution, risk enforcement)
**Python** → Backtesting & research (historical data analysis, backtesting, ML models, strategy optimization)

---

## Development Principles

1. **Risk First**: All code must enforce 1% max risk per trade
2. **Config-Driven**: Strategies and instruments are configurable, not hardcoded
3. **Instrument-Agnostic**: Core logic works for any trading instrument
4. **Human-in-the-Loop**: LLM suggests, human approves, system executes
5. **Brutal Honesty**: Log failures, edge cases, and ugly truths
6. **Use the Right Tool**: Live trading = Node.js, Backtesting/ML = Python

---

## Project Structure

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
├── data/                    # Shared historical and tick data
├── logs/                    # Shared trade logs and backtest results
└── journal/                 # SQLite trade journal database
```

---

## Tech Stack

**Node.js (Live Trading)**
- Runtime: Node.js
- LLM: Ollama (Qwen 2.5 Coder 7B / Phi-3 Mini) - local inference via DirectML
- Broker API: Deriv WebSocket API
- Indicators: technicalindicators npm package

**Python (Research)**
- Backtesting: Backtrader / VectorBT
- Analysis: Pandas, NumPy
- ML: scikit-learn, XGBoost
- Notebooks: Jupyter

**Shared**
- Database: SQLite (trade journal)
- Data: CSV/Parquet files in `data/`
- Config: JSON files in `config/`

---

## Code Style & Constraints

1. **No hardcoded symbols** - Use config files
2. **Risk validation** - Every trade must pass through `node/core/risk-engine.js`
3. **Everything gets logged** - Wins, losses, errors, decisions
4. **Error handling** - Graceful failures, never silent
5. **Comments** - Explain WHY, not WHAT (for future-you)
6. **Language choice** - Live trading in Node.js, backtesting/ML in Python

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

## Prohibited

- Hardcoded trading symbols
- Risk percentage > 1%
- Silent error swallowing
- Trading without backtested edge
- Quick-money mindset
- Writing backtests in Node.js when Python is more suitable

---

## Files to Reference for Context

- `PERPLEXITY_SPACE_SYSTEM_PROMPT.md` - Full project strategy and phases
- `node/config/v75-mean-reversion.js` - Current strategy configuration
- `node/core/risk-engine.js` - Risk validation logic
- `python/requirements.txt` - Python dependencies

---

## When in Doubt

Conservative over aggressive. Data over opinion. Discipline over hype.
