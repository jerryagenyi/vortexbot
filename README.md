# VortexBot

> Algorithmic mean-reversion trading bot with AI decision layer. Instrument-agnostic, config-driven, **hybrid Python/Node.js architecture**.

## Current Focus

**VIX 75 (Deriv) Mean-Reversion Strategy** - Phase 1: Hardware Audit & Model Setup

## Philosophy

Conservative, disciplined, data-driven. No hype.

- **Risk First**: 1% max risk per trade, enforced by code
- **Human-in-the-Loop**: LLM suggests, human approves, system executes
- **Brutal Honesty**: Prove or disprove the edge exists. Both outcomes are valuable.

## Hybrid Architecture

This project uses **two languages for different purposes**:

| Component | Language | Purpose |
|-----------|----------|---------|
| **Live Trading** | Node.js | Deriv WebSocket, real-time streaming, order execution, risk enforcement |
| **Backtesting & Research** | Python | Historical data analysis, backtesting, ML models, strategy optimization |

**Why this split?**
- Python has mature backtesting libraries (Backtrader, VectorBT, Pandas, NumPy) for research
- Node.js excels at async I/O and real-time WebSocket connections for live trading

## Project Structure

```
vortexbot/
├── node/                    # Node.js live trading
│   ├── config/              # Strategy and instrument configurations
│   ├── instruments/         # Deriv symbol mappings
│   ├── strategies/          # Reusable strategy logic
│   ├── core/                # Backtester, risk-engine, trade-logger
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

## Tech Stack

### Node.js (Live Trading)
| Component | Technology |
|-----------|-----------|
| Runtime | Node.js |
| Indicators | technicalindicators |
| WebSocket | ws |
| Broker API | Deriv WebSocket API |
| LLM | Ollama (Qwen 2.5 Coder 7B / Phi-3 Mini) |
| Inference | DirectML (AMD RX 6800 XT) |

### Python (Backtesting & Research)
| Component | Technology |
|-----------|-----------|
| Backtesting | Backtrader / VectorBT |
| Analysis | Pandas / NumPy |
| ML | scikit-learn / XGBoost |
| Notebooks | Jupyter |

### Shared
| Component | Technology |
|-----------|-----------|
| Database | SQLite |
| Data Format | CSV / Parquet |
| Config | JSON |

## Three-Phase Plan

| Phase | Duration | Goal |
|-------|----------|------|
| **Phase 1** | Weeks 1-2 | Hardware Audit & Model Setup |
| **Phase 2** | Weeks 3-8 | MVP & Backtesting |
| **Phase 3** | Months 4-15 | Real Capital Testing ($500 start) |

## Success Definition

12+ consecutive months of profitability (after Phase 2 proof).

**Failure is also a valid outcome**: If no edge exists by month 12, redirect effort to other work.

## Documentation

- **[PERPLEXITY_SPACE_SYSTEM_PROMPT.md](./PERPLEXITY_SPACE_SYSTEM_PROMPT.md)** - Complete project strategy, phases, and brutal honesty protocol
- **[CLAUDE.md](./CLAUDE.md)** - Developer guidance for Claude Code

## License

MIT

---

*Built with discipline. No hype.*
