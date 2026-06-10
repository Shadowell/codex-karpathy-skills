---
name: bitpro-strategy-research
description: Use when researching, writing, validating, backtesting, paper-running, or live-trading BitPro strategies through the BitPro MCP tools. Covers real K-line data sync, BaseStrategy code constraints, backtest matrix workflows, simulation gates, and high-risk live trading confirmation boundaries.
---

# BitPro Strategy Research

Use this skill when a model needs to research, write, validate, backtest, iterate, or deploy a BitPro strategy through MCP. The skill guides tool usage only; it must not bypass BitPro risk controls or directly execute trading outside MCP.

## Core Rules

- Use MCP as the single tool entry: inspect `bitpro_capabilities`, then call focused tools such as `sync_start_history`, `backtest_start_job`, `paper_start`, and `trading_futures_order`.
- Strategy names must follow `[资产类型][K线周期][策略类型] 标的范围 · 策略名称 · 资金版本`.
- Generated code must implement the project `BaseStrategy` contract and pass `strategy_validate_code` before any backtest or runtime use.
- Agent 研发新策略的默认路径是动态 DB 策略：用 `strategy_create(script_content=...)` 保存 `BaseStrategy` 代码，服务端会标记 `strategy_source=db_script` / `script_content_source=db` 并优先加载数据库代码；迭代已有动态策略时用 `strategy_update` 原地更新并保留同一个 `strategy_id`；不要写 `backend/app/strategies/*.py`、不要改 registry、不重启服务。
- 不得使用 mock 行情，不得使用 synthetic K 线，不得用随机数据替代真实 OHLCV。数据不足时先同步历史数据或缩短回测区间。
- Default lifecycle is research -> backtest -> paper/simulation. Live trading is a separate high-risk boundary.

## Workflow

1. Inspect capabilities and health with `bitpro_capabilities` and `bitpro_health`.
2. Search existing strategy and market coverage with `strategy_search`, `market_symbols`, `sync_table_stats`, and `sync_status`.
3. If historical K-line coverage is missing, run `sync_start_history` or `sync_one`, then poll `sync_jobs` and `sync_status`.
4. Write or generate strategy code with `strategy_generate` or local model work, then validate with `strategy_validate_code`.
5. For a new strategy, call `strategy_create` with canonical `name`, `symbols`, `script_content`, and config including `strategy_source=db_script`, `script_content_source=db`, `market_type`, `timeframe`, `is_paper_trading=true`, and paper capital. For an existing dynamic strategy, call `strategy_get` first, then `strategy_update` with changed `script_content`, `config`, name, or symbols. Use the returned `strategy_id`; do not require a BitPro service restart.
6. Run a backtest matrix with `backtest_start_job` across required symbols, timeframes, costs, and recent out-of-sample ranges. Poll `backtest_get_job`, then inspect `backtest_list_results` and `backtest_get_result`.
7. Iterate only from real backtest evidence. Record rejected variants and why.
8. For paper execution, configure with `paper_configure`, start with `paper_start`, and observe via `paper_dashboard`, `paper_events`, and `paper_equity_curve`.
9. Consider live tools only after explicit human approval, successful paper observation, and a passing `live_preflight`.

## Live Trading Boundary

Live mutation tools are high risk. They are disabled unless the MCP process runs with `BITPRO_MCP_ENABLE_LIVE_TRADING=1`.

Every live mutation, including `live_promote`, `trading_spot_order`, `trading_futures_order`, `trading_cancel_order`, and `trading_transfer`, must include:

- `confirm_live_risk=true`
- `confirmation="I_UNDERSTAND_REAL_TRADING_RISK"`
- a non-empty `reason`
- a unique `idempotency_key`

Do not infer or auto-fill live confirmation for the user. Read-only live diagnostics such as balance, positions, open orders, and preflight may be used for analysis, but any real account mutation requires the explicit fields above and must be auditable.
